<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner i VMM skyer til Azure | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du overfører Hyper-V virtuelle maskiner på Hyper-V-værter, der er placeret i System Center VMM skyer til Azure."
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
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Gentage Hyper-V virtuelle maskiner i VMM skyer til Azure

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmm-to-azure.md)
- [PowerShell - ressourcestyring](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Klassisk Portal](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - klassisk](site-recovery-deploy-with-powershell.md)



Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md).

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du kan installere gendannelse af websteder for at gentage Hyper-V virtuelle maskiner på Hyper-V hostservere, der findes i VMM privat skyer til Azure.

I artiklen indeholder forudsætninger for dette scenario, og hvordan du kan konfigurere en samling af legitimationsoplysninger gendannelse af websteder, få provideren Azure websted gendannelse installeret på VMM kildeserveren, registrere serveren i samling af legitimationsoplysninger, tilføje en konto til Azure-lager, installere Azure gendannelse Services agent på Hyper-V værtsservere, konfigurere indstillingerne for dokumentbeskyttelse for VMM sky, der anvendes på alle beskyttede virtuelle maskiner , og derefter aktivere beskyttelse af disse virtuelle computere. Færdigt ved at teste den sekundære at sikre, at alt fungerer som forventet.

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arkitektur

![Arkitektur](./media/site-recovery-vmm-to-azure-classic/topology.png)

- Azure websted gendannelse udbyder er installeret på VMM under gendannelse af websteder installation og VMM serveren er registreret i samling af legitimationsoplysninger gendannelse af websteder. Udbyderen kommunikerer med gendannelse af websteder til at håndtere gentagelse organisering.
- Agent for Azure gendannelse Services er installeret på Hyper-V værtsservere under installation af gendannelse af websteder. Heltal replikering af data til Azure-lager.


## <a name="azure-prerequisites"></a>Azure forudsætninger

Her er, hvad du skal bruge i Azure.

**Nødvendige program** | **Detaljer**
--- | ---
**Azure-konto**| Du skal bruge en [Microsoft Azure](https://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**Azure-lager** | Du skal bruge en Azure-lager-konto til at gemme replikerede data. Replikerede data er gemt i Azure-lager og Azure FOS er spundet op, når der opstår failover. <br/><br/>Du har brug for en [standard geografisk overflødige lagerplads konto](../storage/storage-redundancy.md#geo-redundant-storage). Kontoen skal i det samme område som tjenesten gendannelse af websteder, og at blive knyttet til den samme abonnement. Bemærk, at replikering til premium lagerplads konti understøttes ikke i øjeblikket og ikke bør bruges.<br/><br/>[Læs om](../storage/storage-introduction.md) Azure-lager.
**Azure-netværk** | Du skal have et Azure virtuelle netværk, der Azure FOS opretter forbindelse til, når der opstår failover. Azure virtuelt netværk skal være i samme område som samling af legitimationsoplysninger gendannelse af websteder.

## <a name="on-premises-prerequisites"></a>Forudsætninger for lokalt

Her er, hvad du skal bruge i det lokale miljø.

**Nødvendige program** | **Detaljer**
--- | ---
**VMM** | Du skal have mindst én VMM server installeres som en enkeltstående fysisk eller virtuel server eller en virtuel klynge. <br/><br/>VMM serveren skal køre System Center 2012 R2 med de nyeste akkumulerede opdateringer.<br/><br/>Du skal have mindst én skyen konfigureret på VMM serveren.<br/><br/>Skyen kilde, du vil beskytte skal indeholde en eller flere VMM host grupper.<br/><br/>Få flere oplysninger om konfiguration af VMM skyer i [Gennemgang: oprette private skyer med System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) på Keith Mayer blog.
**Hyper-V** | Du skal bruge en eller flere Hyper-V værtsservere eller grupper i VMM skyen. Host serveren bør have og en eller flere FOS. <br/><br/>Hyper-V server skal køre på mindst **Windows Server 2012 R2** med Hyper-V rollen eller **Microsoft Hyper-V Server 2012 R2** og har installeret de seneste opdateringer.<br/><br/>En hvilken som helst Hyper-V-server, der indeholder FOS, du vil beskytte skal være placeret i en VMM sky.<br/><br/>Hvis du kører Hyper-V i en klynge note, klynge broker ikke er oprettet automatisk Hvis du har en statisk IP-adresse-baseret klynge. Du skal konfigurere klynge broker manuelt. Du kan [Få mere at vide](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) i Aidan Finn blogindlæg.
**Beskyttet computere** | FOS, du vil beskytte skal overholde [krav til Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## <a name="network-mapping-prerequisites"></a>Forudsætninger, der er tilknytning netværk
Når du har beskyttet virtuelle maskiner i Azure netværk tilknytning kort mellem VM netværk på VMM kildeserveren og målrette Azure netværk for at aktivere følgende:

- Alle de computere, hvilke failover på det samme netværk kan oprette forbindelse til hinanden, uanset hvilken gendannelse plan de er i.
- Hvis en netværksgateway er sat op på destinationen Azure netværk, virtuelle computere kan oprette forbindelse til andre lokale virtuelle computere.
- Hvis du ikke konfigurerer netværk tilknytning kun virtuelle maskiner, der ikke består i den samme plan for gendannelse vil ikke kunne oprette forbindelse til hinanden efter failover til Azure.

Hvis du vil installere kort over netværk skal du bruge følgende:

- De virtuelle maskiner, du vil beskytte på VMM kildeserveren skal forbindes til et VM netværk. Dette netværk skal være knyttet til en logisk netværk, der er knyttet til skyen.
- En Azure-netværk, kan oprette forbindelse replikerede virtuelle maskiner efter failover. På tidspunktet ydelse skal du markere dette netværk. Netværket skal være i samme område som abonnementet Azure gendannelse af websteder.

Forberede netværk tilknytning på følgende måde:

1. [Læs om](site-recovery-network-mapping.md) netværk tilknytningskrav.
2. Forberede VM netværk i VMM:

    - [Konfigurere logiske netværk](https://technet.microsoft.com/library/jj721568.aspx).
    - [Konfigurere VM netværk](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Trin 1: Oprette en samling af legitimationsoplysninger gendannelse af websteder

1. Log på [Portalen til administration](https://portal.azure.com) fra serveren VMM du vil registrere.
2. Klik på **Data Services** > **gendannelse Services** > **websted gendannelse samling**.
3. Klik på **Opret et nyt** > **Hurtig oprettelse**.
4. Angiv et fuldt navn til at identificere samling af legitimationsoplysninger i **navnet**.
5. Vælg det geografiske område for samling af legitimationsoplysninger i **område**. Sådan kontrollerer du se understøttede områder geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Klik på **Opret samling**.

    ![Ny samling](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Kontrollere vises på statuslinjen for at bekræfte, at samling af legitimationsoplysninger blev oprettet. Samling af legitimationsoplysninger, vises som **aktiv** på hovedsiden gendannelsestjenester.

## <a name="step-2-generate-a-vault-registration-key"></a>Trin 2: Opret en samling af legitimationsoplysninger registreringsnøgle

Oprette en registreringsnøgle i samling af legitimationsoplysninger. Når du henter Azure websted gendannelse udbyder og installere den på serveren, der VMM, skal du bruge denne tast til at registrere VMM serveren i samling af legitimationsoplysninger.

1. Klik på samling af legitimationsoplysninger for at åbne siden Hurtig Start på siden **Gendannelsestjenester** . Også du kan åbne Hurtig Start når som helst ved hjælp af ikonet.

    ![Ikon for Hurtig Start](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. Vælg **mellem en lokal VMM websted og Microsoft Azure**på rullelisten.
3. Klik på **Generer registreringsnøgle** filer i **Forberede VMM servere**. Filen vigtige genereres automatisk og er gyldige for 5 dage, efter den er oprettet. Hvis du ikke har adgang til portalen Azure fra serveren VMM skal du kopiere denne fil på serveren.

    ![Registreringsnøgle](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Trin 3: Installer Azure websted gendannelse udbyder

1. I **Hurtig Start** > **forberede VMM servere**, skal du klikke på **Hent Microsoft Azure websted gendannelse Provider til installation på VMM servere** for at hente den nyeste version af installationsfilen udbyder.
2. Køre denne fil på VMM kildeserveren.

    >[AZURE.NOTE] Hvis VMM er implementeret i en klynge, og du installerer provideren for første gang installere det på en aktive node og fuldføre installationen for at registrere VMM serveren i samling af legitimationsoplysninger. Installer udbyderen på de andre noder. Bemærk, at hvis du opgraderer udbyderen du skal opgradere på alle noder, fordi de skal alle køre den samme version som udbyder.

3. Installationsprogrammet kontrollerer prerequirements og anmoder om tilladelse til at stoppe tjenesten VMM for at starte installationen af udbyder. Tjenesten VMM genstartes automatisk, når installationen er færdig. Hvis du installerer på en VMM klynge, bliver du bedt om at stoppe rollen klynge.

4. I **Microsoft Update** kan du vælge i efter opdateringer. Når denne indstilling er aktiveret udbyder opdateringer installeres ifølge din Microsoft Update-politik.

    ![Microsoft-opdateringer](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  Installationsplaceringen for udbyderen, der er angivet til ** <SystemDrive>\Programmer\Microsoft System Center 2012 R2\Virtual maskine Manager\bin**. Klik på **Installer**.

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. Klik på **Registrer** for at registrere serveren i samling af legitimationsoplysninger, når udbyderen, der er installeret.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. Bekræft navnet på den samling, hvor serveren, der skal registreres i **samling navn**. Klik på *Næste*.

    ![Registrering af Server](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. Angiv, hvordan den udbyder, der kører på serveren VMM der oprettes forbindelse til internettet i **Internetforbindelse** . Vælg **Opret forbindelse til eksisterende proxyindstillinger** bruge Internet standardindstillingerne for forbindelsen konfigureret på serveren.

    ![Internet-indstillinger](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

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

13.  Klik på **Næste** for at fuldføre processen. Efter registreringen hentes metadata fra serveren VMM ved Azure gendannelse af websteder. Serveren, der vises under fanen **VMM servere** på siden **servere** i samling af legitimationsoplysninger.

    ![LastPage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Efter registreringen hentes metadata fra serveren VMM ved Azure gendannelse af websteder. Serveren, der vises under fanen **VMM servere** på siden **servere** i samling af legitimationsoplysninger.

### <a name="command-line-installation"></a>Kommandolinjen installation

Azure websted gendannelse udbyder kan også installeres ved hjælp af følgende kommandolinjen. Denne metode kan bruges til at installere udbyderen på en Server Core til Windows Server 2012 R2.

1. Hente installation fil- og registreringsoplysninger nøglen Provider til en mappe. For eksempel: C:\ASR.
2. Stoppe tjenesten System Center Virtual Machine Manager
3. Udtrække udbyder installationsprogrammet med disse kommandoer fra en kommandoprompt:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installér dataprovideren på følgende måde:

        C:\ASR> setupdr.exe /i

5. Registrere provideren på følgende måde:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Hvor er parametre på følgende måde:

 - **/Credentials** : obligatorisk parameter, der angiver den placering, hvor den vigtige registreringsdatabasefil er placeret  
 - **/FriendlyName** : obligatorisk parameter for navnet på den server, Hyper-V-vært, der vises i portalen Azure gendannelse af websteder.
 - **/EncryptionEnabled** : valgfri parameter til at angive, om du vil kryptering din virtuelle maskiner i Azure (i resten kryptering). Filnavnet har filtypenavnet **.pfx** .
 - **/proxyAddress** : valgfri parameter, der angiver adressen på proxyserveren.
 - **/proxyport** : valgfri parameter, der angiver porten af proxy-server.
 - **/proxyUsername** : valgfri parameter, der angiver brugernavnet proxy.
 - **/proxyPassword** : valgfri parameter, der angiver adgangskoden, proxy.  


## <a name="step-4-create-an-azure-storage-account"></a>Trin 4: Oprette en Azure-lager-konto

1. Hvis du ikke har en Azure lagerplads konto skal du klikke på **Tilføj en Azure lagerplads konto** til at oprette en konto.
2. Opret en konto med geografisk-gentagelse aktiveret. Det skal i det samme område som tjenesten Azure gendannelse af websteder, og at blive knyttet til den samme abonnement.

    ![Lagerplads konto](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] [Overførsel af lagerplads konti](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke for lagerplads konti, der bruges til implementering af gendannelse af websteder.

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Trin 5: Installer Azure betroet Services bruger

Installer Azure gendannelse Services agent på hver Hyper-V host server i VMM skyen.

1. Klik på **Hurtig Start** > **hente Azure websted betroet Services bruger og Installér på værter** til at hente den nyeste version af filen agent installation.

    ![Installere betroet Services bruger](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. Kør installationsfilen på hver Hyper-V host server.
3. Klik på **Næste**på siden **Forudsætninger Check** . Eventuelle manglende krav installeres automatisk.

    ![Forudsætninger betroet Services bruger](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. Angiv, hvor du vil installere agenten og vælg den cacheplacering, hvor der skal installeres sikkerhedskopiering af metadata, på siden **Indstillinger for Installation** . Klik derefter på **Installer**.
5. Klik på **Luk** for at fuldføre guiden, når installationen er fuldført.

    ![Registrere MARS Agent](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Kommandolinjen installation

Du kan også installere Microsoft Azure betroet Services bruger fra kommandolinjen ved hjælp af denne kommando:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Trin 6: Konfigurere skyen indstillingerne for dokumentbeskyttelse

Når VMM serveren er registreret, kan du konfigurere indstillingerne for dokumentbeskyttelse skyen. Indstillingen **Synkroniser skyen data med samling af legitimationsoplysninger** aktiveret, da du installerede udbyderen, så alle skyer på serveren VMM vises under fanen <b>Beskyttede elementer</b> i samling af legitimationsoplysninger.

![Publicerede skyen](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Klik på **Konfigurer beskyttelse for VMM skyer**på siden Hurtig Start.
2. Klik på fanen **Beskyttede elementer** på i skyen, du vil konfigurere og gå til fanen **konfiguration** .
3. Vælg **Azure**i **mål** .
4. Vælg kontoen Azure-lager, du bruger til gentagelse i **Lagerplads konto** .
5. Angiv **Krypter gemt data** til **fra**. Denne indstilling angiver, at dataene skal krypteres replikeres mellem lokale websted og Azure.
6. Lad standardindstillingen i **Kopier frekvens** . Denne værdi angiver, hvor ofte dataene skal synkroniseres mellem kilde- og destinationswebsteder placeringer.
7. Lad standardindstillingen i **Behold gendannelse point for**. Kun den seneste gendannelsespunkt til en primær virtuel maskine er gemt på en host replikeringsserver med en standardværdi på nul.
8. Lad standardindstillingen i **hyppigheden for programmet ensartet snapshots**. Denne værdi angiver, hvordan ofte til at oprette øjebliksbilleder. Snapshots Brug skygge tjenesten VSS (Volume Copy) til at sikre, at programmer er i en ensartet tilstand, når snapshot er taget.  Hvis du angiver en værdi, skal du kontrollere den er mindre end antallet yderligere betroet punkter, du konfigurerer.
9. I **Gentagelse starttidspunkt**, angive, når indledende replikering af data til Azure skal starte. Tidszone på Hyper-V host serveren bruges. Vi anbefaler, at du planlægger den første replikering belastet.

    ![Replikeringsindstillinger i skyen](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Når du har gemt indstillingerne for et job oprettes og kan overvåges på fanen **job** . Alle Hyper-V værtsservere i VMM kilde skyen konfigureres til gentagelse.

Når du har gemt, kan skyen indstillingerne ændres under fanen **Konfigurer** . Hvis du vil ændre den destinationsplacering eller target lagerplads konto skal du fjerne skyen konfigurationen, og derefter omkonfigurere i skyen. Bemærk, at hvis du ændrer kontoen lagerplads ændringen kun anvendes for virtuelle maskiner, der er aktiveret for beskyttelse, når lagerplads konto er blevet ændret. Eksisterende virtuelle maskiner bliver ikke overflyttet til den nye konto lagerplads.

## <a name="step-7-configure-network-mapping"></a>Trin 7: Konfigurer tilknytning af netværk
Før du begynder at bekræfte kort over netværk, virtuelle maskiner på VMM kildeserveren har forbindelse til en VM netværk. Desuden oprette en eller flere Azure virtuelle netværk. Bemærk, at flere VM netværk kan knyttes til en enkelt Azure netværk.

1. Klik på **kort netværk**på siden Hurtig Start.
2. På fanen **netværk** i **kildeplacering**skal du vælge VMM kildeserveren. Vælg Azure i **destinationsplacering** .
3. I **kilde** netværk vises en liste over VM netværk, der er knyttet til VMM serveren. I **Target** netværk vises de Azure netværk, der er knyttet til abonnementet.
4. Vælg kilde VM netværket, og klik på **kort**.
5. Vælg destinationen Azure netværk, du vil bruge, på siden **Vælg en destination netværk** .
6. Klik på markeringen i afkrydsningsfeltet for at fuldføre tilknytningsprocessen.

    ![Replikeringsindstillinger i skyen](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Når du har gemt indstillingerne for et job begynder at spore tidsplanens status tilknytning og den kan overvåges på fanen job. En hvilken som helst eksisterende replika virtuelle maskiner, der svarer til kilde VM netværk, der skal knyttes til destinationen Azure netværk. Nye virtuelle maskiner, der er forbundet til kilde VM netværket skal have forbindelse til det tilknyttede Azure netværk efter gentagelse. Hvis du redigerer en eksisterende tilknytning med et nyt netværk, forbindes replika virtuelle maskiner ved hjælp af de nye indstillinger.

Bemærk, at hvis target netværk har flere undernet og én af disse undernet har samme navn som undernet virtuelt kilde er placeret, og derefter virtuelt replika der skal knyttes til den destinationsadresse-undernet efter failover. Hvis der er ingen target undernet med et tilsvarende navn skal forbindes den virtuelle maskine til det første undernet i netværket.

> [AZURE.NOTE] [Overførsel af netværk](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke af netværk, der bruges til implementering af gendannelse af websteder.

## <a name="step-8-enable-protection-for-virtual-machines"></a>Trin 8: Aktivér beskyttelse af virtuelle maskiner

Når servere, skyer og netværk er konfigureret korrekt, kan du aktivere beskyttelse af virtuelle maskiner i skyen. Bemærk følgende:

- Virtuelle maskiner skal opfylde [Azure krav](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Hvis du vil aktivere beskyttelse operativsystem og operativsystem skal diskegenskaber være angivet til den virtuelle maskine. Når du opretter en virtuel maskine i VMM ved hjælp af et virtuelt skabelon kan du angive egenskaben. Du kan også angive disse egenskaber for eksisterende virtuelle maskiner under fanen **Generelt** og **Hardwarekonfiguration** virtuelt egenskaber. Hvis du ikke angiver disse egenskaber i VMM vil du kunne konfigurere dem i portalen Azure gendannelse af websteder.

    ![Oprette virtuelle maskine](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Rediger egenskaberne for virtuelt](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. Hvis du vil aktivere beskyttelse under fanen **virtuelle maskiner** i skyen, hvor den virtuelle maskine er placeret, skal du klikke på **Aktivér beskyttelse** > **Tilføj virtuelle computere**.
2. På listen over virtuelle maskiner i skyen, Vælg det, du vil beskytte.

    ![Aktiver virtuelt beskyttelse](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Registrere status for handlingen **Aktivér beskyttelse** i fanen **job** , herunder den første replikering. Når du har kørt **Færdiggøre beskyttelse** jobbet er den virtuelle maskine klar til failover. Når beskyttelse er aktiveret og virtuelle maskiner replikeres, vil du kunne se dem i Azure.


    ![Virtuelt beskyttelse job](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. Bekræft egenskaberne virtuelt og ændre efter behov.

    ![Bekræfte virtuelle maskiner](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. Under fanen **konfiguration** af egenskaberne virtuelt kan følgende egenskaber for netværk ændres.





- **Antal netværkskort på en målliste virtuel maskine** - antallet af netværkskort, bestemmes af den størrelse, du angiver for virtuelt mål. Se [virtuelt størrelse specifikationer](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) for antallet af kort, der understøttes af virtuelt størrelse. Når du ændrer størrelsen på til en virtuel maskine og gemme indstillingerne, ændres antallet netværkskort, når du åbner siden **Konfigurer** næste gang. Antallet af netværkskort af target virtuelle maskiner er det mindste antal netværkskort på kilde virtuel maskine og det maksimale antal netværkskort, der understøttes af størrelsen på den virtuelle maskine, der er valgt, på følgende måde:

    - Hvis antallet af netværkskort på maskinen kilde er mindre end eller lig med antallet af kort, der er tilladt for target maskine størrelse, derefter får destinationen det samme antal kort som kilde.
    - Hvis antallet af kort til den kilde virtuelle maskine overstiger det antal, der er tilladt for mål størrelsen og derefter mål størrelsen maksimum der skal bruges.
    - Hvis en kilde maskine har to netværkskort og mål maskine størrelsen understøtter fire, har target maskinen for eksempel to kort. Hvis kildecomputer har to kort, men understøttede mål størrelsen understøtter kun en har target maskinen kun ét kort.    

- **Netværk af virtuelt mål** -, den virtuelle maskine opretter forbindelse til netværket bestemmes af netværk tilknytningen af netværket på kilde virtuelle maskine. Hvis virtuelt kilde har mere end ét netværkskort og kilde netværk er tilknyttet forskellige netværk på destinationen, derefter skal du vælge mellem en af mål netværkene.
- **Undernet af hvert netværkskort** - For hvert netværkskort, kan du vælge undernettet, den mislykkedes over virtuelt vil oprette forbindelse til.
- **Target IP-adresse** - Hvis netværkskort på kilde virtuelle maskine er konfigureret til at bruge en statisk IP-adresse, og derefter kan du angive IP-adressen til den destinationsadresse virtuelle maskine. Brug denne funktion bevare IP-adressen på en kilde virtuel maskine efter en failover. Hvis ingen IP-adresse ikke er angivet gives alle tilgængelige IP-adresser til netværksadapter på tid ydelse. Hvis target IP-adressen er angivet, men der allerede bruges af en anden virtuel maskine, der kører i Azure mislykkes failover.  

    ![Rediger egenskaberne for netværket](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] Linux virtuelle maskiner med statiske IP-adresse understøttes ikke.

## <a name="test-the-deployment"></a>Test installationen

For at teste din installation kan du køre en test sekundær server til en enkelt virtuel maskine, eller Opret en gendannelse plan bestående af flere virtuelle maskiner og køre en test sekundær server til planen.  

Test failover simulerer din failover og gendannelse ordning i en isolerede netværk. Bemærk, at:

- Hvis du vil oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord efter sekundære, skal du aktivere Fjernskrivebord på den virtuelle maskine, før du kører sekundære test.
- Du skal bruge en offentlig IP-adresse til at oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord efter failover. Hvis du gør dette, skal du sikre, at du ikke har en hvilken som helst domæne politikker, der forhindrer dig i at oprette forbindelse til en virtuel maskine, ved hjælp af en offentlig adresse.

>[AZURE.NOTE] Sørg for, at du har installeret Azure Agent i den beskyttede computer for at få den bedste ydeevne, når du gør en failover til Azure. Dette kan i starte hurtigere og også, at i diagnose i tilfælde af problemer. Linux agent kan være fundet [her](https://github.com/Azure/WALinuxAgent) - og Windows agent kan findes [her](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>Oprette en plan for gendannelse

1. Tilføje en ny plan under fanen **Gendannelse planer** . Angiv et navn, **VMM** i **Kildetype**og VMM kildeserveren i **kilden**, destinationen bliver Azure.

    ![Oprette en plan](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. Vælg virtuelle maskiner til at føje til den gendannelse plan på siden **Vælg virtuelle computere** . Disse virtuelle maskiner er føjet til standardgruppen gendannelse plan – gruppe 1. Maksimalt 100 virtuelle maskiner i en enkelt gendannelse plan er testet.

- Hvis du vil kontrollere virtuelt egenskaberne før du føjer dem til planen, skal du klikke på den virtuelle maskine på egenskabssiden for skyen hvor det er placeret. Du kan også konfigurere egenskaberne virtuelt i konsollen VMM.
- Alle de virtuelle maskiner, der vises, er blevet aktiveret for beskyttelse. Listen indeholder begge virtuelle maskiner, der er aktiveret til beskyttelse og første replikering er fuldført, og dem, der er aktiveret for beskyttelse med første gentagelse afventer. Kun virtuelle maskiner med indledende gentagelse fuldført kan mislykkes som en del af en plan for gendannelse.

    ![Oprette en plan](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Når en gendannelse plan er blevet oprettet det vises på fanen **Gendannelse planer** . Du kan også tilføje [Azure automatisering runbooks](site-recovery-runbook-automation.md) til gendannelse plan til at automatisere handlinger under failover.

### <a name="run-a-test-failover"></a>Køre en test failover

Der er to måder at køre en test failover til Azure.

- **Teste failover uden et netværk med Azure**– denne type test ydelse kontrollerer, at den virtuelle maskine dukker op korrekt i Azure. Den virtuelle maskine forbindelse ikke til en hvilken som helst Azure netværk efter failover.
- **Teste failover med et netværk med Azure**– denne type failover Kontroller, at det hele gentagelse miljø kommer op som forventet, og som mislykkedes over virtuelle maskiner, der skal knyttes til den angivne destination Azure netværk. For håndtering af undernet, til test vil failover undernet af virtuelt test blive fundet ud af baseret på undernet af virtuelt replika. Dette er forskellige på almindelig replikering, når undernettet af en replika virtuel maskine er baseret på undernettet virtuelt kilde.

Hvis du vil køre en test sekundær server til en virtuel maskine, der er aktiveret for beskyttelse til Azure uden at angive et Azure target netværk, du ikke behøver at forberede noget. Hvis du vil køre en test failover med en destination Azure netværk skal du oprette et nyt Azure netværk, der har identificeret fra netværket Azure fremstilling (standardfunktionsmåden, når du opretter et nyt netværk i Azure). Se, hvordan man kan [køre en test failover](site-recovery-failover.md#run-a-test-failover) få mere at vide.


Du skal også konfigurere infrastrukturen for den replikerede virtuelle maskine fungerer som forventet. For eksempel en virtuel maskine med domænecontrolleren og DNS kan replikeres til Azure ved hjælp af Azure gendannelse af websteder og kan oprettes i test netværket med teste Failover. Se på [teste active directory failover overvejelser i forbindelse med](site-recovery-active-directory.md#considerations-for-test-failover) sektionen få mere at vide.

For at køre en test failover at benytte følgende fremgangsmåde:

1. Vælg planen, og klik på **Test Failover**under fanen **Gendannelse planer** .
2. Vælg **ingen** eller et bestemt Azure netværk på siden **Bekræft Test Failover** .  Bemærk, at hvis du vælger ingen test sekundære skal kontrollere, at den virtuelle maskine replikeres korrekt til Azure men ikke kontrollerer din gentagelse netværkskonfiguration.

    ![Ingen netværk](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. Hvis kryptering af data er aktiveret til skyen, i **Krypteringsnøgle** , skal du markere det certifikat, der er udstedt under installationen af provideren på serveren VMM, når du har aktiveret mulighed for at aktivere kryptering af data til en skybaseret.
4. Du kan spore failover fremdrift på fanen **job** . Du skal også kunne se den virtuelle maskine test replika i portalen Azure. Du kan starte en Fjernskrivebord forbindelse til den virtuelle maskine, hvis du er sat op til access virtuelle maskiner fra dit lokale netværk.
5. Klik på **Fuldført teste** at afslutte op sekundære test, når sekundære Når fasen **færdigt test** . Du kan analysere ned til fanen **Job** til at registrere failover fremskridt og status samt til at udføre handlinger, der skal bruges.
6. Efter failover vil du kunne se den virtuelle maskine test replika i portalen Azure. Du kan starte en Fjernskrivebord forbindelse til den virtuelle maskine, hvis du er sat op til access virtuelle maskiner fra dit lokale netværk. Benyt følgende fremgangsmåde:

    1. Kontrollér, at de virtuelle maskiner starte uden fejl.
    2. Hvis du vil oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord efter sekundære, skal du aktivere Fjernskrivebord på den virtuelle maskine, før du kører sekundære test. Du skal også tilføje et RDP slutpunkt på en virtuel maskine. Du kan udnytte en [Azure automatisering Runbooks](site-recovery-runbook-automation.md) for at gøre dette.
    3. Når failover, hvis du bruger en offentlig IP-adresse til at oprette forbindelse til den virtuelle maskine i Azure ved hjælp af Fjernskrivebord, sikre kan du ikke har en hvilken som helst domæne politikker, der forhindrer dig i at oprette forbindelse til en virtuel maskine, ved hjælp af en offentlig adresse.

7.  Benyt følgende fremgangsmåde, når afprøvning er fuldført:
    - Klik på **sekundære test er fuldført**. Rydde op i testmiljø automatisk sluk og slette test virtuelle computere.
    - Klik på **noter** for at registrere og gemme de bemærkninger, der er knyttet til sekundære test.

>

## <a name="next-steps"></a>Næste trin

Få mere at vide om [konfiguration af gendannelse planer](site-recovery-create-recovery-plans.md) og [belastningsjustering](site-recovery-failover.md).
