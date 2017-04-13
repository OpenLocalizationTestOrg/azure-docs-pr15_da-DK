<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner i VMM skyer til Azure ved hjælp af gendannelse af websteder ved hjælp af Azure portal | Microsoft Azure"
    description="Beskriver, hvordan du kan installere Azure gendannelse af websteder for at dirigerer gentagelse, failover og gendannelse af Hyper-V FOS i VMM skyer til Azure ved hjælp af portalen Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Gentage Hyper-V virtuelle maskiner i VMM skyer til Azure ved hjælp af Azure gendannelse af websteder ved hjælp af Azure portal | Microsoft Azure

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmm-to-azure.md)
- [Azure klassisk](site-recovery-vmm-to-azure-classic.md)
- [PowerShell ressourcestyring](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell klassisk](site-recovery-deploy-with-powershell.md)

Velkommen til gendannelse af Azure websteder! Brug denne artikel, hvis du vil gentage lokale Hyper-V virtuelle maskiner administreres i System Center Virtual Machine Manager (VMM) skyer til Azure ved hjælp af Azure gendannelse af websteder i portalen Azure.

> [AZURE.NOTE]Azure har to forskellige [installation modeller](../resource-manager-deployment-model
> ) til oprettelse og arbejde med ressourcer: Azure ressourcestyring og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller.


Azure gendannelse af websteder i portalen Azure indeholder adskillige nye funktioner:

- På portalen Azure kombineres tjenesterne Azure sikkerhedskopiering og gendannelse af Azure websteder til en enkelt gendannelse Services samling, så du kan konfigurere og administrere Forretningskontinuitet og nedbrud (BCDR) fra ét sted. Et samlet dashboard kan du overvåge og administrere handlinger på tværs af dine lokale websteder og Azure offentlige skyen.
- Brugere med Azure abonnementer klargjort med programmet skyen løsning CSP'EN (Provider) kan nu administrere gendannelse af websteder handlinger i portalen Azure.
- Gendannelse af websteder i portalen Azure kan replikeres maskiner til Azure ressourcestyring lagerplads konti. Gendannelse af websteder opretter på failover, ressourcestyring-baserede FOS i Azure.
- Gendannelse af websteder fortsat understøtter gentagelse til klassisk lagerplads konti. På failover opretter gendannelse af websteder FOS ved hjælp af klassisk modellen.


Når du har læst i denne artikel, kan du sende kommentarer nederst i Disqus kommentarer. Bed tekniske spørgsmål på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Oversigt

Virksomheder har brug for en strategi for BCDR, der bestemmer, hvordan apps, arbejdsmængder og data holde køre og være tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normal arbejdsbetingelser så tidligt som muligt. Din strategi for BCDR skal beholde forretningsdata sikre og kan gendannes, og sikre, at arbejdsbelastninger forbliver konstant er tilgængelig, når der udsendes nedbrud.

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR ved orchestrating gentagelse af lokale fysiske servere og virtuelle maskiner til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår afbrydelser i din primære placering, du ikke sekundær placeringen for at holde apps og arbejdsbelastninger, som er tilgængelige. Du ikke tilbage til din primære placering, når det vender tilbage til normal handlinger. Se mere i [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

Denne artikel indeholder alle de oplysninger, du vil gentage lokale Hyper-V FOS i VMM skyer til Azure. Den indeholder en oversigt over arkitekturen, planlægning oplysninger og installation trin til konfiguration af Azure, lokale servere, Replikeringsindstillinger og kapacitetsplanlægning af. Du kan aktivere gentagelse på computere, du vil beskytte, og Markér failover fungerer, når du har konfigureret infrastrukturen.


## <a name="business-advantages"></a>Business fordele

- Gendannelse af websteder giver beskyttelse for business arbejdsmængder og programmer, der kører på Hyper-V FOS.
- Gendannelse Services-portalen for at oprette, administrere og overvåge gentagelse, failover og genoprettelse på ét sted.
- Du kan nemt køre failover fra din lokale infrastruktur Azure og failback (Gendan) fra Azure Hyper-V værtsservere i dit lokale websted.
- Du kan konfigurere gendannelse planer med flere computere, så lagdelte arbejdsbelastninger mislykkes sammen.


## <a name="scenario-architecture"></a>Scenarie arkitektur


Dette er de scenarie komponenter:

- **VMM server**: en lokal VMM server med en eller flere skyer.
- **Hyper-V host eller klynge**: Hyper-V værtsservere eller grupper, der administreres i VMM skyer.
- **Azure websted gendannelse udbyder og tjenester betroet**: under installationen du har installeret Azure websted gendannelse provideren på VMM server, og tjenester til Microsoft Azure genoprettelse agent på Hyper-V værtsservere. Udbyderen på serveren VMM kommunikerer med gendannelse af websteder over HTTPS 443 at gentage organisering. Agent på Hyper-V host serveren kopieres data til Azure-lager over HTTPS 443 som standard.
- **Azure**: Du skal et Azure-abonnement, en Azure-lager-konto til replikeres til lagring af data og en Azure virtuelt netværk, så Azure FOS er tilsluttet et netværk efter failover.

![E2A topologi](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Azure forudsætninger

Her er, hvad du skal i Azure til at installere dette scenario.

**Nødvendige program** | **Detaljer**
--- | ---
**Azure-konto**| Du har brug for en [Microsoft Azure](http://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**Azure-lager** | Du har brug for en standard Azure lagerplads konto for at gemme replikerede data. Du kan bruge en LRS eller GRS lagerplads-konto. Vi anbefaler GRS, så dataene er tolerant, hvis der opstår en internationale afbrydelse, eller hvis det primære område ikke kan gendannes. [Få mere at vide](../storage/storage-redundancy.md). Kontoen skal være i samme område som gendannelse Services samling.<br/><br/>Premium lagerplads understøttes ikke.<br/><br/> Replikerede data er gemt i Azure-lager og Azure FOS oprettes, når der opstår failover. <br/><br/> [Læs om](../storage/storage-introduction.md) Azure-lager.
**Azure-netværk** | Du har brug for en Azure virtuelt netværk, Azure FOS opretter forbindelse til, når der opstår failover. Netværket skal være i samme område som gendannelse Services samling.

## <a name="on-premises-prerequisites"></a>Forudsætninger for lokalt

Her er, hvad du skal i det lokale miljø

**Nødvendige program** | **Detaljer**
--- | ---
**VMM**| En eller flere VMM servere, der kører på System Center 2012 R2. Hver VMM server skal have en eller flere skyer, der er konfigureret. En sky, som skal indeholde:<br/><br/> En eller flere VMM host grupper.<br/><br/> En eller flere Hyper-V værtsservere eller grupper i hver host gruppe.<br/><br/>Du kan [Få mere at vide](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) om konfiguration af VMM skyer.
**Hyper-V** | Hyper-V værtsservere skal køre mindst **Windows Server 2012 R2** med Hyper-V-rollen eller **Microsoft Hyper-V Server 2012 R2** og har installeret de seneste opdateringer.<br/><br/> En Hyper-V server skal indeholde et eller flere FOS.<br/><br/> En Hyper-V host server eller en klynge, der indeholder FOS, du vil gentage skal administreres i en VMM sky.<br/><br/>Hyper-V-servere skal forbindes til internettet, enten direkte eller via en proxy.<br/><br/>Hyper-V servere bør have løsninger, der er nævnt i artikel [2961977](https://support.microsoft.com/kb/2961977) installeret.<br/><br/>Hyper-V værtsservere have adgang til internettet for at udføre datareplikering Azure.
**Udbyder og agent** | Under installationen af Azure gendannelse af websteder skal du installere Azure websted gendannelse udbyder på VMM server, og tjenester til genoprettelse agent på Hyper-V hosts. Provider og agent har du brug at oprette forbindelse til Azure via internettet direkte eller via en proxy. Bemærk, at en HTTPS-baserede proxy ikke understøttes. Proxy-server på VMM server og Hyper-V hosts skal give adgang til: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blob.core.windows.net <br/><br/> *. store.core.windows.net<br/><br/>Hvis du har IP-adresse-baserede firewallregler på VMM serveren, kan du kontrollere, at reglerne, der tillader kommunikation til Azure. Du har brug for til at tillade [Azure Datacenter IP-områder](https://www.microsoft.com/download/confirmation.aspx?id=41653) og HTTPS (443)-port.<br/><br/>Tillad IP-adresseområder for det Azure område for dit abonnement og for Vest USA.<br/><br/>Derudover. proxy-server på serveren VMM skal have adgang til https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>Forudsætninger, der er beskyttet computer


**Nødvendige program** | **Detaljer**
--- | ---
**Beskyttet FOS** | Før du ikke over en VM, skal du kontrollere, at det navn, der er tildelt til Azure VM overholder [Azure forudsætninger](site-recovery-best-practices.md#azure-virtual-machine-requirements). Du kan ændre navnet, når du har aktiveret replikering af VM. <br/><br/> Individuelle diskkapacitet på beskyttet maskiner ikke bør være mere end 1023 GB. En VM kan have op til 16 diske (dermed op til 16 TB).<br/><br/> Delt disk gæst klynger ikke understøttes.<br/><br/> Unified Extensible Firmware Interface UEFI () / Extensible Firmware Interface(EFI) start understøttes ikke.<br/><br/> Hvis kilden VM har NIC samarbejde konverteres til en enkelt NIC efter failover til Azure.<br/><br/>Beskytte VM'er, der kører Linux med en statisk IP-adresse, understøttes ikke.

## <a name="prepare-for-deployment"></a>Forberede til installation

Sådan forbereder du dig til installation, skal du:

1. [Konfigurere et netværk med Azure](#set-up-an-azure-network) , Azure FOS er placeret efter failover.
2. [Konfigurere en konto til Azure lagerplads](#set-up-an-azure-storage-account) for replikerede data.
4. [Forberede VMM serveren](#prepare-the-vmm-server) til installation af gendannelse af websteder.
5. [Klargør til kort over netværk](#prepare-for-network-mapping). Konfigurere netværk, så du kan konfigurere netværk tilknytning under installation af gendannelse af websteder.

### <a name="set-up-an-azure-network"></a>Konfigurere et netværk med Azure

Du skal et netværk med Azure, så Azure FOS oprettet, efter failover opretter forbindelse til den.

- Netværket skal være i samme område som den, hvori du installerer samling af legitimationsoplysninger gendannelsestjenester.
- Afhængigt af ressource model, du vil bruge til mislykkedes over Azure FOS, skal du konfigurere det Azure netværk i [ressourcestyring tilstand](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [tilstanden Klassisk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Vi anbefaler, at du konfigurerer et netværk, inden du går i gang. Hvis du ikke gør det, skal du gøre det under installationen af gendannelse af websteder.

> [AZURE.NOTE] [Overførsel af netværk](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke af netværk, der bruges til implementering af gendannelse af websteder.


### <a name="set-up-an-azure-storage-account"></a>Konfigurere en konto til Azure-lager

- Du skal bruge en standard Azure lagerplads konto til at indeholde data replikeres til Azure. Kontoen skal være i samme område som gendannelse Services samling.
- Afhængigt af ressource model, du vil bruge til mislykkedes over Azure FOS, konfigurere du en konto i [ressourcestyring tilstand](../storage/storage-create-storage-account.md) eller [tilstanden Klassisk](../storage/storage-create-storage-account-classic-portal.md).
- Vi anbefaler, at du konfigurere en konto, før du går i gang. Hvis du ikke gør det, skal du gøre det under installationen af gendannelse af websteder.

> [AZURE.NOTE] [Overførsel af lagerplads konti](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke for lagerplads konti, der bruges til implementering af gendannelse af websteder.

### <a name="prepare-the-vmm-server"></a>Forberedelse af VMM-serveren

- Sørg for, at serveren VMM overholder [forudsætninger](#on-premises-prerequisites).
- Under installationen af gendannelse af websteder, kan du angive, at alle skyer på en server, VMM skal være tilgængelig i portalen Azure. Hvis du kun vil bestemte skyer skal vises i portalen, kan du aktivere indstillingen på skyen i administrationskonsollen VMM.


### <a name="prepare-for-network-mapping"></a>Forberede netværk tilknytning

Du skal konfigurere netværk tilknytning under installation af gendannelse af websteder. Netværk tilknytning knytter mellem kilde VMM VM-netværk og målrette Azure netværk for at give mulighed for følgende:

- Computere, der ikke på det samme netværk kan oprette forbindelse til hinanden, selvom de ikke er mislykkedes på samme måde eller i den samme plan for gendannelse.
- Hvis en netværksgateway er konfigureret på destinationen Azure netværk, Azure virtuelle computere kan oprette forbindelse til lokale virtuelle computere.
- Hvis du vil konfigurere netværk er tilknytning her hvad du har brug at forberede:

    - Sørg for, at FOS på kildeserveren Hyper-V host er tilsluttet et VMM VM netværk. Dette netværk skal være knyttet til en logisk netværk, der er knyttet til skyen.
    - Et Azure netværk som beskrevet [ovenfor](#set-up-an-azure-network)

- Du kan [Få mere at vide](site-recovery-network-mapping.md) om, hvordan netværk tilknytning fungerer.


## <a name="create-a-recovery-services-vault"></a>Oprette en samling af legitimationsoplysninger gendannelse

1. Log på [Azure-portalen](https://portal.azure.com).
2. Klik på **nyt** > **Management** > **gendannelsestjenester**. Alternativt kan du klikke på **Gennemse** > **Gendannelse Services** vaults > **Tilføj**.

    ![Ny samling](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. Angiv et navn til at identificere samling af legitimationsoplysninger i **navnet**. Hvis du har mere end ét abonnement, skal du vælge en af dem.
4. [Opret en ressourcegruppe](../resource-group-template-deploy-portal.md), eller Vælg et eksisterende dokument. Angiv et Azure område. Computere kan replikeres til dette område. Understøttede områder se for at kontrollere geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Hvis du vil hurtigt få adgang til samling af legitimationsoplysninger fra dashboardet, klikke på **Fastgør til dashboard** > **Opret samling**.

    ![Ny samling](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

Den nye samling af legitimationsoplysninger vises på **dashboardet** > **alle de ressourcer**, og primære **gendannelse Services vaults** blade.

## <a name="getting-started"></a>Kom godt i gang

Gendannelse af websteder giver en introduktion oplevelse, der hjælper dig med at installere så hurtigt som muligt. Kom godt i gang kontrollerer forudsætninger og vejleder dig gennem installation trinnene i den rigtige rækkefølge gendannelse af websteder.

Komme i gang du vælge typen af maskiner, du vil gentage, og hvor du kan replikeres til. Du kan konfigurere lokale servere, Azure lagerplads konti og netværk. Du opretter gentagelse politikker og udfører kapacitetsplanlægning. Når du har konfigureret din infrastruktur kan du aktivere gentagelse til FOS. Du kan køre failover for bestemte maskiner, eller Opret gendannelse planer mislykkes over flere computere.

Begynde at komme i gang ved at vælge, hvordan du vil installere gendannelse af websteder. Kom godt i gang strømmen ændres en smule afhængigt af dine krav til gentagelse.



## <a name="step-1-choose-your-protection-goals"></a>Trin 1: Vælg dine beskyttelse mål

Vælg, hvad du vil gentage og hvor du kan replikeres til.

1. Vælg din samling bladet **gendannelse Services vaults** og klik på **Indstillinger**.
2. **Komme** i gang skal du klikke på **Gendannelse af websteder** > **trin 1: forberede infrastruktur** > **beskyttelse mål**.

    ![Vælg mål](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. Vælg **Til Azure**i **målet for beskyttelse** , og vælg **Ja, med Hyper-V**. Vælg **Ja** for at bekræfte, at du bruger VMM til at administrere Hyper-V værter og webstedet gendannelse. Klik derefter på **OK**.

    ![Vælg mål](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>Trin 2: Konfigurere kildemiljøet

Installer Azure websted gendannelse udbyder på VMM serveren, og registrere serveren i samling af legitimationsoplysninger. Installer Azure gendannelse Services agent på Hyper-V hosts.

1. Klik på **trin 2: forberede infrastruktur** > **kilde**.

    ![Konfigurere kilde](./media/site-recovery-vmm-to-azure/set-source1.png)

2. Klik på **+ VMM** for at tilføje en VMM server i **forberede kilde** .

    ![Konfigurere kilde](./media/site-recovery-vmm-to-azure/set-source2.png)

3. I den **Tilføj Server** blade check, **System Center VMM server** vises i **servertype** og, der opfylder VMM serveren [forudsætninger og URL-adressen krav](#on-premises-prerequisites).
4. Hente installationsfilen til Azure websted gendannelse udbyder.
5. Hent tasten registrering. Du skal bruge denne, når du kører installationsprogrammet. Tasten er gyldigt i fem dage, når du opretter den.

    ![Konfigurere kilde](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Installer Azure websted gendannelse udbyder på VMM serveren.


### <a name="set-up-the-azure-site-recovery-provider"></a>Konfigurere provideren Azure websted gendannelse

1.  Kør provideren installationsfilen.
2. I **Microsoft Update** kan du vælge i efter opdateringer, så udbyder opdateringer installeres i overensstemmelse med din Microsoft Update-politik.
3. **Installation**, acceptere eller ændre standardplaceringen udbyder og klik på **Installer**.

    ![Installere placering](./media/site-recovery-vmm-to-azure/provider2.png)

4. Klik på **Registrer** for at registrere VMM serveren i samling af legitimationsoplysninger, når installationen er fuldført.
5. Klik på **Gennemse** for at markere filen samling vigtige på siden **Indstillinger for samling af legitimationsoplysninger** . Angiv abonnementets Azure gendannelse af websteder og navnet, samling af legitimationsoplysninger.

    ![Registrering af Server](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. Angiv, hvordan den udbyder, der kører på serveren VMM opretter forbindelse til gendannelse af websteder via internettet i **Internetforbindelse**.

    - Vælg **Opret forbindelse direkte til Azure gendannelse af websteder uden en proxy**, hvis du vil hos udbyderen for at oprette forbindelse direkte.
    - Hvis din eksisterende proxy kræver godkendelse, eller du vil bruge en brugerdefineret proxy skal du markere **Opret forbindelse til Azure gendannelse af websteder ved hjælp af en proxy-server**.
    - Hvis du bruger en brugerdefineret proxy, angive den adresse, port og legitimationsoplysninger.
    - Hvis du bruger en proxy, du har allerede må URL-adresser, der er beskrevet i [forudsætninger](#on-premises-prerequisites).
    - Hvis du bruger en brugerdefineret proxy oprettes VMM RunAs-konto (DRAProxyAccount) automatisk ved hjælp af de angivne proxylegitimationsoplysninger. Konfigurere proxyserveren, så denne konto kan godkende korrekt. Kontoindstillinger VMM RunAs kan redigeres i konsollen VMM. I **Indstillinger**, udvid **sikkerhed** > **Køre som konti**, og derefter ændre adgangskoden til DRAProxyAccount. Du skal genstarte tjenesten VMM, så indstillingen træder i kraft.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Acceptere eller ændre placeringen af et SSL-certifikat, der genereres automatisk til kryptering af data. Dette certifikat bruges, hvis du aktiverer kryptering af data til en sky, der er beskyttet af Azure i portalen Azure gendannelse af websteder. Beskyt dette certifikat. Når du kører en failover til Azure, du skal bruge den til at dekryptere, hvis kryptering af data er aktiveret.


8. I **servernavn**skal du angive et fuldt navn til at identificere VMM server på samling af legitimationsoplysninger. Angiv VMM klynge Rollenavnet i en klynge konfiguration.
9. Aktivere **synkronisering skyen metadata** , hvis du vil synkronisere metadataene for alle skyer på serveren VMM med samling af legitimationsoplysninger. Denne handling skal kun udføres én gang på hver server. Hvis du ikke vil synkronisere alle skyer, kan du lade denne indstilling ikke er markeret og synkronisere hver skyen enkeltvis i skyen-egenskaber i konsollen VMM. Klik på **Registrer** for at fuldføre processen.

    ![Registrering af Server](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. Registrering starter. Når registrering afsluttes, skal serveren, der vises på siden **Indstillinger for** > **servere** blade i samling af legitimationsoplysninger.


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Kommandolinjen installation til Azure websted gendannelse udbyder

Azure websted gendannelse udbyder kan installeres fra kommandolinjen. Denne metode kan bruges til at installere udbyderen på Server Core til Windows Server 2012 R2.

1. Hente installation fil- og registreringsoplysninger nøglen Provider til en mappe. For eksempel C:\ASR.
2. Kør følgende kommandoer til at uddrage udbyder installer fra en kommandoprompt:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Kør denne kommando for at installere komponenterne:

            C:\ASR> setupdr.exe /i

4. Kør derefter disse kommandoer til at registrere serveren i samling af legitimationsoplysninger:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Hvor:

- **/Credentials**: obligatorisk parameter, der angiver, hvor den vigtige registreringsdatabasefil er placeret.  
- **/FriendlyName**: obligatorisk parameter for navnet på den server, Hyper-V-vært, der vises i portalen Azure gendannelse af websteder.
- - **/EncryptionEnabled**: valgfri parameter, når du replikering Hyper-V FOS i VMM skyer til Azure. Angiv, om du vil kryptere virtuelle maskiner i Azure (i resten kryptering). Sørg for, at navnet på filen har filtypenavnet **.pfx** . Kryptering er som standard deaktiveret.
- **/proxyAddress**: valgfri parameter, der angiver adressen på proxyserveren.
- **/proxyport**: valgfri parameter, der angiver porten af proxy-server.
- **/proxyUsername**: valgfri parameter, der angiver brugernavnet proxy (hvis proxy kræver godkendelse).
- **/proxyPassword**: valgfri parameter, der angiver adgangskoden, du vil godkende med proxy-server (hvis proxyen, der kræver godkendelse).


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installer Azure gendannelse Services agent på Hyper-V hosts

1. Når du har konfigureret udbyderen, skal du hente installationsfilen til Azure gendannelse Services agent. Kør installationsprogrammet på hver Hyper-V server i VMM skyen.

    ![Hyper-V websteder](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Klik på **Næste**på siden **Forudsætninger Check** . Eventuelle manglende krav installeres automatisk.

    ![Forudsætninger betroet Services bruger](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. På siden **Indstillinger for Installation** Acceptér eller ændre placeringen af installationen og placeringen af cachen. Du kan konfigurere cachen på et drev, der har mindst 5 GB lagerplads, men vi anbefaler et cache drev med 600 GB eller mere ledig plads. Klik derefter på **Installer**.
4. Når installationen er fuldført, skal du klikke på **Luk** at afslutte.

    ![Registrere MARS Agent](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Kommandolinjen installation til Azure websted gendannelse Services agent

Du kan installere Microsoft Azure betroet Services bruger fra kommandolinjen ved hjælp af følgende kommando:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Konfiguration af proxy internetadgang til gendannelse af websteder fra Hyper-V værter

Den gendannelse Services agent, der kører på Hyper-V-værter kræver internetadgang til Azure til VM gentagelse. Hvis du har adgang til internettet via en proxy, konfigurere det på følgende måde:

1. Åbn snap-in Microsoft Azure sikkerhedskopi MMC på værten Hyper-V. En genvej til Microsoft Azure sikkerhedskopiering er tilgængelige på skrivebordet eller på C:\Program Files\Microsoft Azure gendannelse Services Agent\bin\wabadmin som standard.
2. Klik på **Skift egenskaber**i snap-in.
3. Angiv oplysninger om proxy-server under fanen **Konfiguration af Proxy** .

    ![Registrere MARS Agent](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Sørg for, at agenten kan få fat i de URL-adresser, der er beskrevet i [forudsætninger](#on-premises-prerequisites).


## <a name="step-3-set-up-the-target-environment"></a>Trin 3: Konfigurere miljøet mål

Angiv Azure lagerplads kontoen skal bruges til replikering og Azure netværket som Azure FOS vil oprette forbindelse efter failover.

1.  Klik på **Forbered infrastruktur** > **mål** og vælg det Azure abonnement, du vil bruge.
2.  Angiv den implementeringsmodel, du vil bruge til FOS efter failover.
3.  Gendannelse af websteder kontrollerer, at du har en eller flere kompatible Azure lagerplads konti og netværk.

    ![Lagerplads](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Hvis du ikke har oprettet en lagerplads-konto, og du vil oprette en ved hjælp af Ressourcestyring, klikke på **+ lagerplads konto** til at gøre indbygget.  Angive et kontonavn, type, abonnement og placering på bladet **Opret lagerplads konto** . Kontoen skal være i samme placering, som samling af legitimationsoplysninger gendannelsestjenester.

    ![Lagerplads](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Bemærk, at:

    - Hvis du vil oprette en lagerplads konto ved hjælp af klassisk modellen, skal gøre du det på portalen Azure. [Lær mere](../storage/storage-create-storage-account-classic-portal.md)
    - Hvis du bruger en premium lagerplads konto til replikerede data, skal du konfigurere en ekstra standard lagerplads konto til at gemme logfiler over gentagelse, hente igangværende ændringer til lokale data.

4.  Hvis du ikke har skabt et netværk med Azure, og du vil oprette en ved hjælp af Ressourcestyring klikke på **+ netværk** til at gøre indbygget. Angiv et netværksnavn, adresseområde, undernet detaljer, abonnement og placering på bladet **Opret virtuelt netværk** . Netværket skal være i samme placering, som samling af legitimationsoplysninger gendannelsestjenester.

    ![Netværk](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Hvis du vil oprette et netværk med klassisk modellen skal du gøre det på portalen Azure. [Få mere at vide](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Konfigurer tilknytning af netværk

- [Læse](#prepare-for-network-mapping) et hurtigt overblik over hvilke kort over netværk understøtter. [Læse dette](site-recovery-network-mapping.md) til en mere detaljeret beskrivelse.
- Kontrollér, at virtuelle maskiner på VMM serveren er tilsluttet et VM netværk og, om du har oprettet mindst én Azure virtuelt netværk. Flere VM netværk kan knyttes til en enkelt Azure netværk.

Konfigurere tilknytning på følgende måde:

1. I **Indstillinger for** > **Websted gendannelse infrastruktur** > **netværk tilknytninger** > **Tilknytning af netværk**, skal du klikke på ikonet **+ netværk tilknytning** .

    ![Kort over netværk](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Vælg VMM kildeserveren og **Azure** som mål på **Tilføj netværk tilknytning** .
3. Bekræft abonnementet og implementeringsmodel efter failover.
4. Vælg i **kilde netværk**, du vil tilknytte på listen, der er knyttet til serveren, der VMM kilde lokale VM netværk.
5. Vælg Azure netværket hvilke replika Azure FOS vil være placeret når de er oprettet i **Target netværk**. Klik derefter på **OK**.

    ![Kort over netværk](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Her er, hvad der sker, når netværk tilknytning begynder:

- Eksisterende FOS på kilde VM netværket er forbundet til destinationsnetværket, når tilknytning begynder. Ny FOS forbindelse til datakilde VM netværk har forbindelse til den tilknyttede Azure netværk, når replikering forekommer.
- Hvis du redigerer en eksisterende netværk tilknytning, forbindes replika virtuelle maskiner ved hjælp af de nye indstillinger.
- Hvis target netværket har flere undernet og én af disse undernet har samme navn som undernet, hvor virtuelt kilde er placeret, der derefter virtuelt replika skal knyttes til pågældende target undernet efter failover.
- Hvis der er ingen target undernet med et tilsvarende navn skal forbindes den virtuelle maskine til det første undernet i netværket.



## <a name="step-4-set-up-replication-settings"></a>Trin 4: Konfigurere indstillinger for gentagelse


1. Hvis du vil oprette en ny politik for gentagelse, skal du klikke på **Forbered infrastruktur** > **Replikeringsindstillinger** > **+ Opret og Tilknyt**.

    ![Netværk](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. Angiv navn på en politik under **oprette og knytte politik**.
3. I **kopiere hyppighed**, skal du angive, hvor ofte du vil gentage delta data efter den indledende gentagelse (hver 30 sekunder, 5 eller 15 minutter).
4. I **gendannelse Peg opbevaring**, angive i timer hvor lang tid vinduet opbevaring bliver for hvert gendannelsespunkt. Beskyttet computere kan gendannes til et punkt i et vindue.
6. I **App-ensartet øjebliksbillede hyppighed**, angive, hvor ofte (1-12 timer) gendannelse punkter, der indeholder programmet ensartet snapshots bliver oprettet. Hyper-V anvender to typer af snapshots – et standard øjebliksbillede, der indeholder en trinvis øjebliksbillede af hele virtuelt og et program-ensartet øjebliksbillede, der tager et punkt-in-time øjebliksbillede af programmet dataene i den virtuelle maskine. Program-ensartet snapshots Brug skygge tjenesten VSS (Volume Copy) til at sikre, at programmer er i en ensartet tilstand, når snapshot er taget. Bemærk, at hvis du aktiverer programmet ensartet snapshots, vil det påvirke ydeevnen for programmer, der kører på kilde virtuelle maskiner. Sørg for, at den værdi, du angiver er mindre end antallet yderligere betroet punkter, du konfigurerer.
3. I **indledende gentagelse starttidspunkt**, angive, hvornår du skal starte den indledende gentagelse. Replikering forekommer over din internetbåndbredde, så du kan vælge at planlægge det uden for din optaget timer.
5. I **Krypter data, der er gemt på Azure**, Angiv, om til at kryptere resten data i Azure-lager. Klik derefter på **OK**.

    ![Politikken til replikering](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Når du opretter en ny politik er den automatisk knyttet til VMM skyen. Klik på **OK**. Du kan knytte yderligere VMM skyer (og FOS i dem) til denne politik skal skrives gentagelse i **Indstillinger for** > **Gentagelse** > navn på politik > **Knytte VMM skyen**.

    ![Politikken til replikering](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Trin 5: Kapacitetsplanlægning

Nu hvor du har dit basic infrastruktur konfigurere du kan synes om kapacitetsplanlægning af og finde ud af, om du har brug for yderligere ressourcer.

Gendannelse af websteder giver en kapacitet planlægger for at hjælpe dig med at tildele de rette ressourcer til kilde-miljø, websted gendannelse komponenter, netværk og opbevaring. Du kan køre planlæggeren i hurtigtilstand for skøn baseret på en gennemsnitlige antal FOS, diske og lagerplads eller i detaljeret tilstand, skal du angive figurer på niveauet for arbejdsbyrde. Før du går i gang skal du:

- Indsamle oplysninger om det gentagelse miljø, herunder FOS, diske per FOS og lager per disk.
- Anslå daglige ændring (transportspand) du har for replikerede data. Du kan bruge [kapacitet planner til Hyper-V replika](https://www.microsoft.com/download/details.aspx?id=39057) kan hjælpe dig med at gøre dette.

1.  Klik på **Hent** for at hente værktøjet og derefter køre den. [Læse artiklen](site-recovery-capacity-planner.md) , der følger med værktøjet.
2.  Når du er færdig Vælg **Ja** , **du har kørt kapacitet planlæggeren**?

    ![Planlægning af kapacitet](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Overvejelser i forbindelse med netværksbåndbredde

Du kan bruge værktøjet kapacitet teamplanlægning til at beregne den båndbredde, du har brug for til replikering (indledende gentagelse og derefter delta). Hvis du vil styre mængden af båndbredde til replikering har du et par muligheder:

- **Begrænse båndbredde**: Hyper-V trafik, der kopieres til et sekundært websted gennemgår en bestemt Hyper-V-vært. Du kan begrænse båndbredde på host serveren.
- **Tilpasse båndbredde**: Du kan påvirke den båndbredde, der bruges af replikering ved hjælp af et par registreringsdatabasenøgler.

#### <a name="throttle-bandwidth"></a>Begrænse båndbredde

1. Åbne snap-in Microsoft Azure sikkerhedskopi MMC på Hyper-V host serveren. En genvej til Microsoft Azure sikkerhedskopiering er tilgængelige på skrivebordet eller på C:\Program Files\Microsoft Azure gendannelse Services Agent\bin\wabadmin som standard.
2. Klik på **Skift egenskaber**i snap-in.
3. Under fanen **Throttling** Vælg **Aktiver brug af internetbåndbredde (throttling) for sikkerhedskopiering**og angive begrænsninger for arbejde og ikke-arbejde timer. Gyldige områder er fra 512 Kbps til 102 Mbps sekundet.

    ![Begrænse båndbredde](./media/site-recovery-vmm-to-azure/throttle2.png)

Du kan også bruge cmdlet'en [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) til at angive (throttling). Her er et eksempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Angiv OBMachineSetting NoThrottle** angiver, at ingen (throttling) er påkrævet.


#### <a name="influence-network-bandwidth"></a>Påvirke netværksbåndbredde

Værdien **UploadThreadsPerVM** i registreringsdatabasen styrer antallet af tråde, der bruges til dataoverførsel (de oprindelige eller delta replikering) på en disk. En højere værdi forøger netværksbåndbredde for gentagelse. Værdien i registreringsdatabasen **DownloadThreadsPerVM** angiver antallet tråde, der bruges til dataoverførsel under failback.

1. Gå til **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**i registreringsdatabasen.

    - Ændre værdien **UploadThreadsPerVM** (eller oprette nøglen, hvis den ikke findes) til kontrolelementet tråde bruges til disk gentagelse.
    - Ændre værdien **DownloadThreadsPerVM** (eller oprette nøglen, hvis den ikke findes) til kontrolelementet tråde brugt for failback trafik fra Azure.
2. Standardværdien er 4. I en "overprovisioned" netværk, bør disse registreringsdatabasenøgler ændres fra standardværdierne. Maksimum er 32. Overvåge trafik til at optimere værdien.

## <a name="step-6-enable-replication"></a>Trin 6: Aktivér gentagelse

Aktiver nu replikering på følgende måde:

1. Klik på **trin 2: gentage programmet** > **kilde**. Når du har aktiveret replikering for første gang du klikke på **+ gentage** i samling af legitimationsoplysninger til at aktivere gentagelse til flere computere.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. I bladet **kilde** > Vælg VMM serveren og i skyen, hvor Hyper-V-værter er placeret. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. Vælg abonnementet, indlæg failover implementeringsmodel og kontoen lagerplads du bruger til replikerede data i **mål** .

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Vælg kontoen lagerplads, du vil bruge. Hvis du vil bruge en anden lagerplads konto end dem, har du kan [oprette en](#set-up-an-azure-storage-account). Klik på **Opret ny**for at oprette et lager konto ved hjælp af Ressourcestyring modellen. Hvis du vil oprette en lagerplads konto ved hjælp af klassisk modellen, skal gøre du dette [i portalen Azure](../storage/storage-create-storage-account-classic-portal.md). Klik derefter på **OK**.
5. Vælg Azure Netværks- og undernet som Azure FOS opretter forbindelse til, når de er spundet op efter failover. Vælg **Konfigurer nu til markerede computere** at anvende indstillingen på alle computere, du vælger for beskyttelse. Vælg **Konfigurer senere** for at markere det Azure netværk per computer. Hvis du vil bruge et andet netværk fra dem, har du kan [oprette en](#set-up-an-azure-network). Klik på **Opret ny**for at oprette et netværk ved hjælp af Ressourcestyring modellen. Hvis du vil oprette et netværk med klassisk modellen, du bruger [i portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Vælg et undernet, hvis det er relevant. Klik derefter på **OK**.
6. I **virtuelle maskiner** > **Vælg virtuelle maskiner** skal du klikke på og Vælg hver computer, du vil gentage. Du kan kun vælge maskiner, gentagelse kan aktiveres. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. I **Egenskaber for** > **konfigurere egenskaber**, Vælg operativsystemet for de valgte FOS og OS disken. Klik derefter på **OK**. Du kan angive yderligere egenskaber, der senere.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. I **Replikeringsindstillinger** > **konfigurere gentagelse indstillinger**skal du vælge politikken gentagelse, du vil anvende til de beskyttede FOS. Klik derefter på **OK**. Du kan ændre politikken gentagelse i **Indstillinger for** > **replikering politikker** > navn på politik > **Rediger indstillinger**. Ændringer, du anvender bruges til maskiner, der allerede replikering og nye maskiner.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Du kan følge statussen for **Aktivér beskyttelse** jobbet i **Indstillinger for** > **job** > **job gendannelse af websteder**. Når du har kørt **Færdiggøre beskyttelse** jobbet maskinen er klar til failover.

### <a name="view-and-manage-vm-properties"></a>Få vist og administrere VM egenskaber

Vi anbefaler, at du kontrollerer egenskaber for datakilde maskinen. Husk, at Azure VM navn skal overholde med [Azure virtuelt krav](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Klik på **Indstillinger for** > **Beskyttet elementer** > **Replikeres elementer** >, og vælg maskinen til at se detaljerne.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. Du kan få vist gentagelse og belastningsjustering oplysninger for VM i **Egenskaber** .

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. I **Beregn og netværk** > **beregne egenskaber** kan du angive Azure VM navn- og destinationswebsteder størrelse. Ændre navnet, der opfylder kravene [Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) , hvis det er nødvendigt. Du kan også få vist og redigere oplysninger om target netværk, undernet og IP-adresse, der er tildelt til Azure VM. Bemærk følgende:

    - Du kan angive target IP-adresse. Hvis du ikke angiver en adresse, den mislykkedes over maskine skal bruge DHCP. Hvis du angiver en adresse, der ikke er tilgængelig på failover, mislykkes sekundære. Den samme target IP-adresse kan bruges til test failover, hvis adressen, der er tilgængelige i test failover netværket.
    - Antallet af netværkskort, bestemmes af den størrelse, du angiver for virtuelt mål på følgende måde:

        - Hvis antallet af netværkskort på maskinen kilde er mindre end eller lig med antallet af kort, der er tilladt for target maskine størrelse, derefter får destinationen det samme antal kort som kilde.
        - Hvis antallet af kort til kilde virtual machine overstiger det antal, der er tilladt for mål størrelsen, så mål størrelsen maksimum benyttes.
        - Eksempelvis hvis en kilde maskine har to netværkskort og mål maskine størrelsen understøtter fire, target maskinen har to kort. Hvis kildecomputer har to kort, men understøttede mål størrelsen understøtter kun en har target maskinen kun ét kort.     
        - Hvis VM har flere netværkskort de kan alle oprette forbindelse til det samme netværk.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  Du kan se operativsystem og datadisce på VM, der kan replikeres i **diske** .



## <a name="step-7-test-your-deployment"></a>Trin 7: Teste din installation

Du kan køre en test sekundær server til en enkelt virtuel computer eller en gendannelse plan, der indeholder en eller flere virtuelle maskiner for at teste installationen.


### <a name="prepare-for-failover"></a>Forberede failover

- Hvis du vil køre en test failover anbefaler vi, at du opretter et nyt Azure netværk, der har isolerede fra netværket Azure fremstilling (dette er standardfunktionsmåden, når du opretter et nyt netværk i Azure). Du kan [Få mere at vide](site-recovery-failover.md#run-a-test-failover) om at køre test failover.
- Installer Azure Agent på den beskyttede computer for at få den bedste ydeevne, når du ikke Azure. Det gør starte hurtigere og hjælper dig med fejlfinding. Installere [Linux](https://github.com/Azure/WALinuxAgent) eller [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) -agent.
- For at fuldt teste din installation, du har brug for en infrastruktur for replikerede maskinen fungerer som forventet. Du kan oprette en virtuel maskine som et domænenavn fra domænecontrolleren med DNS og replikeres dette til Azure ved hjælp af Azure gendannelse af websteder, hvis du vil teste Active Directory og DNS. Læs mere i [test failover overvejelser i forbindelse med Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Hvis du vil køre en ikke-planlagt failover i stedet for en test failover opmærksom på følgende:

    - Hvis det er muligt skal du lukke primære maskiner før du kører en ikke-planlagt failover. Dette sikrer, at du ikke har både kilde- og replika maskiner, der kører på samme tid.
    - Når du kører en ikke-planlagt failover stopper replikering af data fra primære maskiner, så alle data delta ikke overføres, når en ikke-planlagt failover begynder. Desuden Hvis du kører en ikke-planlagt failover på en plan for gendannelse den kører, indtil fuldført, selvom der opstår en fejl.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Forberede dig på at oprette forbindelse til Azure FOS efter failover

Hvis du vil oprette forbindelse til Azure FOS af RDP-failover, skal du gøre følgende:

**På den lokale computer før failover**:

- Aktivere RDP for adgang via internettet, sikre, at TCP- og UDP-regler er føjet til den **offentlige**, og sikre, at RDP er tilladt i **Windows Firewall** -> **tilladte programmer og funktioner** for alle profiler.
- Aktivere RDP på computeren for at få adgang via en forbindelse til websted, og sikre, at RDP er tilladt i **Windows Firewall** -> **tilladte programmer og funktioner** for **domæne** og **Private** netværk.
- Installer [Azure VM agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) på den lokale computer.
- Sørg for, at det operativsystem SAN politik er indstillet til OnlineAll. [Lær mere]( https://support.microsoft.com/kb/3031135)
- Deaktivere IPSec-tjenesten, før du kører sekundære.

**I feltet Azure VM efter failover**:

- Føje et offentlige slutpunkt for RDP-protokollen (port 3389) og angive legitimationsoplysninger for logon.
- Sikre, at du ikke har en hvilken som helst domæne politikker, der forhindrer dig i at oprette forbindelse til en virtuel maskine, ved hjælp af en offentlig adresse.
- Prøv at oprette forbindelse. Hvis du ikke kan oprette forbindelse, kan du kontrollere, hvor VM kører. Læs denne [artikel](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)for flere tip til fejlfinding.

Hvis du vil have adgang til en Azure VM kører Linux efter failover ved hjælp af en Secure Shell-klient (ssh), kan du gøre følgende:

**På den lokale computer før failover**:

- Sørg for, at tjenesten Secure Shell på Azure VM er indstillet til at starte automatisk ved systemstart.
- Kontrollér, at firewallregler har givet en SSH forbindelse til den.

**I feltet Azure VM efter failover**:

- Netværk sikkerhed gruppe regler på mislykkedes over VM, og det Azure undernet, som den er tilsluttet skal tillade indgående forbindelser til SSH porten.
- Der skal oprettes et offentlige slutpunkt for at tillade indgående forbindelser på SSH porten (TCP-port 22 som standard).
- Hvis VM er tilgængelige via en VPN-forbindelse (Express rute eller til websted VPN) kan klienten bruges til at oprette forbindelse direkte til VM over SSH.


### <a name="run-a-test-failover"></a>Køre en test failover

Sådan køres test failover gør følgende:

1. Mislykkes over en enkelt VM i **Indstillinger for** > **Replikeres elementer**, skal du klikke på VM > **+ Test Failover**.
2. Mislykkes over en gendannelse plan i **Indstillinger for** > **Gendannelse plan**, skal du højreklikke på planen > **Test Failover**. Hvis du vil oprette planlægge en genoprettelse [skal du følge disse instruktioner](site-recovery-create-recovery-plans.md).

3. Vælg Azure netværket, Azure FOS forbinde når serverskift i **Test Failover** .
4. Klik på **OK** for at starte sekundære. Du kan registrere status for opgaver ved at klikke på VM til at åbne dens egenskaber eller på **Test Failover** jobbet i **Indstillinger for** > **job gendannelse af websteder**.
5. Når sekundære Når fasen **færdigt test** , kan du gøre følgende:

    1. Få vist den replika virtuelle maskine i portalen Azure. Kontrollér, at den virtuelle maskine startes korrekt.
    2. Du kan starte en Fjernskrivebord forbindelse til den virtuelle maskine, hvis du er sat op til access virtuelle maskiner fra dit lokale netværk.
    3. Klik på **udført en test** for at afslutte den.
    4. Klik på **noter** for at registrere og gemme de bemærkninger, der er knyttet til sekundære test.
    5. Klik på **sekundære test er fuldført**. Rydde op i testmiljø automatisk sluk og slette virtuelt test.
    6. På dette tidspunkt slettes eventuelle elementer eller VM'er, der er oprettet automatisk af gendannelse af websteder under sekundære test. Du har oprettet for test failover ekstra elementer slettes ikke.

    > [AZURE.NOTE] Hvis en test failover fortsætter mere end to uger, den er fuldført af kraft.

6. Når sekundære er fuldført skal du også kunne se replikaen Azure maskine vises i portalen Azure > **virtuelle computere**. Du skal sikre dig, at VM har den ønskede størrelse, som den er tilsluttet til det pågældende netværk, og at den kører.
7. Hvis du [forberedt for forbindelser efter failover](#prepare-to-connect-to-Azure-VMs-after-failover) du bør kunne oprette forbindelse til Azure VM.


## <a name="monitor-your-deployment"></a>Overvåge installationen

Her er, hvordan du kan overvåge konfigurationsindstillinger, status og tilstand for implementeringen gendannelse af websteder:

1. Klik på navnet på samling af legitimationsoplysninger for at få adgang til dashboardet **Essentials** . I dette dashboard kan du gendannelse af websteder job, gentagelse status, gendannelse planer, servertilstand og begivenheder.  Du kan tilpasse Essentials for at få vist felter og layout, der er bedst egnede til dig, herunder status for andre vaults sikkerhedskopiering og gendannelse af websteder.

    ![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)

2. I feltet **tilstand** kan du overvåge webstedsservere (VMM eller konfiguration servere), der oplever problemet, og de hændelser, der er anledning gendannelse af websteder i de seneste 24 timer.
3. Du kan administrere og overvåge gentagelse i kolonnen **Replikeres elementer**, **Gendannelse planer**, og **Websted gendannelse job** felter. Du kan analysere i sager i **Indstillinger for** -> **job** -> **Websted gendannelse job**.


## <a name="next-steps"></a>Næste trin

Når installationen er konfigureret og kører, kan du [Få mere at vide](site-recovery-failover.md) om forskellige typer ydelse.
