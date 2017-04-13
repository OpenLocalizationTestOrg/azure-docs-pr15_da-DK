<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner (uden VMM) til Azure ved hjælp af Azure gendannelse af websteder ved hjælp af Azure portal | Microsoft Azure"
    description="Beskriver, hvordan du kan installere Azure gendannelse af websteder for at dirigerer gentagelse, failover og gendannelse af lokale Hyper-V VM'er, der ikke administreres af VMM til Azure ved hjælp af portalen Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="raynew"/>


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Gentage Hyper-V virtuelle maskiner (uden VMM) til Azure ved hjælp af Azure gendannelse af websteder ved hjælp af Azure portal

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-hyper-v-site-to-azure.md)
- [Azure klassisk](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell - ressourcestyring](site-recovery-deploy-with-powershell-resource-manager.md)



Velkommen til gendannelse af Azure websteder! Brug denne artikel, hvis du vil gentage lokale Hyper-V virtuelle maskiner, der **ikke** administreres i System Center virtuelle maskiner Manager (VMM) skyer til Azure. I denne artikel beskrives, hvordan du konfigurerer gentagelse ved hjælp af Azure gendannelse af websteder i portalen Azure.

> [AZURE.NOTE] Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer: Azure ressourcestyring og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller.

> Azure gendannelse af websteder understøtter gendannelse og overførsel af Hyper-V virtuelle maskiner til Azure. Trinene i denne artikel gælder identisk under konfigurationen af gentagelse til Azure nedbrud eller for at overføre FOS til Azure

Azure gendannelse af websteder i portalen Azure indeholder en række nye funktioner:

- I Azure kombineres-portalen, Azure sikkerhedskopiering og gendannelse af websteder Azure services til en enkelt gendannelse Services samling så du kan konfigurere og administrere Forretningskontinuitet og nedbrud (BCDR) fra ét sted. Et samlet dashboard kan du overvåge og administrere handlinger på tværs af dine lokale websteder og Azure offentlige skyen.
- Brugere med Azure abonnementer klargjort med programmet skyen løsning CSP'EN (Provider) kan nu administrere gendannelse af websteder handlinger i portalen Azure.
- Gendannelse af websteder i portalen Azure kan replikeres maskiner til Ressourcestyring lagerplads konti. Gendannelse af websteder opretter på failover, ressourcestyring-baserede FOS i Azure.
- Gendannelse af websteder fortsat understøtter gentagelse til klassisk lagerplads konti og belastningsjustering af FOS ved hjælp af den klassiske implementeringsmodel.


I denne artikel sende din feedback nederst i sektionen Disqus kommentarer, efter at have læst. Bed tekniske spørgsmål på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Oversigt


Virksomheder har brug for en strategi for BCDR, der bestemmer, hvordan apps, arbejdsmængder og data holde køre og være tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normal arbejdsbetingelser så tidligt som muligt. Din strategi for BCDR bevarer forretningsdata sikre og kan gendannes, og sikre, at arbejdsbelastninger, som er konstant er tilgængelig, når der udsendes nedbrud.

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR ved orchestrating gentagelse af lokale fysiske servere og virtuelle maskiner til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår udfald i din primære placering, kan du ikke til den sekundære placering til at holde apps og arbejdsbelastninger, som er tilgængelige. Du kan ikke tilbage til din primære placering, når det vender tilbage til normal handlinger. Se mere i [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

Denne artikel indeholder alle de oplysninger, du vil gentage lokale Hyper-V virtuelle maskiner, der **ikke** administreres i System Center virtuelle maskiner Manager (VMM) skyer til Azure. Den indeholder en oversigt over arkitekturen, planlægning oplysninger og installation trin til konfiguration af lokale servere, Azure, en politik for gentagelse og kapacitetsplanlægning. Når du har konfigureret infrastrukturen kan du aktivere gentagelse på computere, du vil beskytte, og Udfør en test failover for at validere konfigureres. Du kan også overføre din FOS til Azure ved først at foretage en planlagt failover og udfør derefter overførslen.

## <a name="business-advantages"></a>Business fordele

- Indeholder (Azure) failover til business arbejdsmængder og programmer, der kører på Hyper-V virtuelle computere.
- Indeholder en enkelt gendannelse Services-konsol til enkel konfiguration og administration af replikering, failover og genoprettelse processer.
- Kan du nemt køre failover fra din lokale infrastruktur til Azure, og fail-tilbage (Gendan) fra Azure til webstedet, i det lokale miljø.
- Du kan konfigurere gendannelse planer med flere computere, så lagdelte arbejdsbelastninger mislykkes sammen.

## <a name="scenario-architecture"></a>Scenarie arkitektur

Dette er de scenarie komponenter:

- **Hyper-V host eller klynge**: lokale Hyper-V værtsservere eller grupper. De Hyper-V værter, der kører FOS, du vil beskytte samles til logiske Hyper-V websteder under installation af gendannelse af websteder.
- **Azure websted gendannelse udbyder og tjenester betroet**: under installationen du installerer Azure websted gendannelse udbyder og tjenester til Microsoft Azure genoprettelse agent på Hyper-V værtsservere. Udbyderen kommunikerer med Azure gendannelse af websteder over HTTPS 443 at gentage organisering. Agent på Hyper-V host serveren kopieres data til Azure-lager over HTTPS 443 som standard.
- **Azure**: Du skal et Azure-abonnement, en Azure-lager-konto til replikeres til lagring af data og en Azure virtuelt netværk, så Azure FOS er tilsluttet et netværk efter failover.

![Hyper-V webstedsarkitektur](./media/site-recovery-hyper-v-site-to-azure/architecture.png)



## <a name="azure-prerequisites"></a>Azure forudsætninger

Her er, hvad du skal bruge i Azure til at installere dette scenario.

**Nødvendige program** | **Detaljer**
--- | ---
**Azure-konto**| Du skal bruge en [Microsoft Azure](http://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**Azure-lager** | Du skal bruge en standard-lager-konto. Du kan bruge en LRS eller GRS lagerplads-konto. Vi anbefaler GRS, så dataene er tolerant, hvis der opstår en internationale afbrydelse, eller hvis det primære område ikke kan gendannes. [Få mere at vide](../storage/storage-redundancy.md). Kontoen skal være i samme område som gendannelse Services samling.<br/><br/> Premium lagerplads understøttes ikke.<br/><br/> Replikerede data er gemt i Azure-lager og Azure FOS oprettes, når der opstår failover.<br/><br/> [Læs om](../storage/storage-introduction.md) Azure-lager.
**Azure-netværk** | Du skal have et Azure virtuelle netværk, der Azure FOS opretter forbindelse til, når der opstår failover. Azure virtuelt netværk skal være i samme område som gendannelse Services samling.

## <a name="on-premises-prerequisites"></a>Forudsætninger for lokalt

Her er, hvad du skal bruge i det lokale miljø.

**Nødvendige program** | **Detaljer**
--- | ---
**Hyper-V** | En eller flere lokale servere, der kører **Windows Server 2012 R2** med seneste opdateringer og Hyper-V-rollen aktiveret eller **Microsoft Hyper-V Server 2012 R2**.<br/><br/>Hyper-V server skal indeholde et eller flere virtuelle computere.<br/><br/>Hyper-V-servere skal forbindes til internettet, enten direkte eller via en proxy.<br/><br/>Hyper-V servere bør have løsninger, der er nævnt i [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") installeret.
**Udbyder og agent** | Under installationen af Azure gendannelse af websteder skal du installere Azure websted gendannelse udbyder. Udbyder installationen installeres også Azure betroet Services bruger på hver Hyper-V server, der kører virtuelle maskiner, du vil beskytte. Alle Hyper-V-servere i en samling af legitimationsoplysninger gendannelse af websteder har de samme versioner af udbyder og agent.<br/><br/>Udbyderen, der skal oprette forbindelse til Azure gendannelse af websteder via internettet. Trafik kan sendes direkte eller via en proxy. Bemærk, at HTTPS baseret proxy understøttes ikke. Proxy-server skal give adgang til: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blog.core.windows.net <br/><br/> *store.Core.Windows.NET <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>Hvis du har IP-adresse-baserede firewallregler på serveren, kan du kontrollere, at reglerne, der tillader kommunikation til Azure. Du skal til at tillade [Azure Datacenter IP-områder](https://www.microsoft.com/download/confirmation.aspx?id=41653) og HTTPS (443)-port.<br/><br/>Tillad IP-adresseområder for det Azure område for dit abonnement og for Vest USA.

## <a name="protected-machine-prerequisites"></a>Forudsætninger, der er beskyttet computer


**Nødvendige program** | **Detaljer**
--- | ---
**Beskyttet FOS** | Før du mislykkes over en VM skal du kontrollere, at det navn, der skal tildeles til Azure VM overholder [Azure forudsætninger](site-recovery-best-practices.md#azure-virtual-machine-requirements). Du kan ændre navnet, når du har aktiveret replikering af VM.<br/><br/> Individuelle diskkapacitet på beskyttet maskiner ikke bør være mere end 1023 GB. En VM kan have op til 16 diske (dermed op til 16 TB).<br/><br/> Delt disk gæst klynger ikke understøttes.<br/><br/> Hvis kilden VM har NIC samarbejde konverteres til en enkelt NIC efter failover til Azure.<br/><br/>Beskytte VM'er, der kører Linux med en statisk IP-adresse, understøttes ikke.

## <a name="prepare-for-deployment"></a>Forberede til installation

Sådan forbereder du dig til installation, skal du:

1. [Konfigurere et netværk med Azure](#set-up-an-azure-network) som Azure FOS vil være placeret, når de er oprettet efter failover.
2. [Konfigurere en konto til Azure lagerplads](#set-up-an-azure-storage-account) for replikerede data.
3. [Forberede Hyper-V hosts](#prepare-the-hyper-v-hosts) for at sikre, at de kan få adgang til de påkrævede URL-adresser.

### <a name="set-up-an-azure-network"></a>Konfigurere et netværk med Azure

Konfigurere et netværk med Azure. Du skal bruge dette, så Azure FOS oprettet, efter failover er tilsluttet et netværk.

- Netværket skal være i samme område som et, skal du installere samling af legitimationsoplysninger gendannelsestjenester.
- Afhængigt af ressource model, du vil bruge til mislykkedes over Azure FOS, skal du konfigurere det Azure netværk i [ressourcestyring tilstand](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [tilstanden Klassisk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Vi anbefaler, at du konfigurerer et netværk, inden du går i gang. Hvis du ikke du skal gøre det under installationen af gendannelse af websteder.

> [AZURE.NOTE] [Overførsel af netværk](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke af netværk, der bruges til implementering af gendannelse af websteder.

### <a name="set-up-an-azure-storage-account"></a>Konfigurere en konto til Azure-lager

- Du skal bruge en standard Azure lagerplads konto til at indeholde data replikeres til Azure.
- Afhængigt af ressource model, du vil bruge til mislykkedes over Azure FOS, skal du konfigurere en konto i [ressourcestyring tilstand](../storage/storage-create-storage-account.md) eller [tilstanden Klassisk](../storage/storage-create-storage-account-classic-portal.md).
- Vi anbefaler, at du konfigurerer en lagerplads-konto, før du går i gang. Hvis du ikke du skal gøre det under installationen af gendannelse af websteder. Kontiene, der skal være i samme område som gendannelse Services samling.

> [AZURE.NOTE] [Overførsel af lagerplads konti](../resource-group-move-resources.md) på tværs af grupper i det samme abonnement eller på tværs af abonnementer understøttes ikke for lagerplads konti, der bruges til implementering af gendannelse af websteder.

### <a name="prepare-the-hyper-v-hosts"></a>Forberede Hyper-V-værter

- Sørg for, at Hyper-V-værter i overensstemmelse med [forudsætninger](#on-premises-prerequisites).

### <a name="create-a-recovery-services-vault"></a>Oprette en samling af legitimationsoplysninger gendannelse

1. Log på [Azure-portalen](https://portal.azure.com).
2. Klik på **nyt** > **Management** > **Sikkerhedskopiering og gendannelse af websteder (OMS)**. Alternativt kan du klikke på **Gennemse** > **Gendannelse Services** vaults > **Tilføj**.

    ![Ny samling](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. Angiv et navn til at identificere samling af legitimationsoplysninger i **navn** . Hvis du har mere end ét abonnement, skal du vælge en af dem.
4. [Opret en ny ressourcegruppe](../resource-group-template-deploy-portal.md) eller Vælg et eksisterende dokument, og Angiv et Azure område. Computere kan replikeres til dette område. Understøttede områder se for at kontrollere geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Hvis du vil hurtigt få adgang til samling af legitimationsoplysninger fra dashboardet klikke på **Fastgør til dashboard** , og klik derefter på **Opret samling**.

    ![Ny samling](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

Den nye samling af legitimationsoplysninger vises på **dashboardet** > **alle de ressourcer**, og primære **gendannelse Services vaults** blade.

## <a name="getting-started"></a>Kom godt i gang

Gendannelse af websteder giver en introduktion oplevelse, der hjælper dig med at installere så hurtigt som muligt. Kom godt i gang kontrollerer forudsætninger og vejleder dig gennem installation trinnene i den rigtige rækkefølge gendannelse af websteder.

Komme i gang du vælge typen af maskiner, du vil gentage, og hvor du kan replikeres til. Du kan konfigurere lokale servere, Azure lagerplads konti og netværk. Du opretter gentagelse politikker og udfører kapacitetsplanlægning. Når du har konfigureret din infrastruktur kan du aktivere gentagelse til FOS. Du kan køre failover for bestemte maskiner, eller Opret gendannelse planer mislykkes over flere computere.

Begynde at komme i gang ved at vælge, hvordan du vil installere gendannelse af websteder. Kom godt i gang strømmen ændres en smule afhængigt af dine krav til gentagelse.



## <a name="step-1-choose-your-protection-goals"></a>Trin 1: Vælg dine beskyttelse mål

Vælg, hvad du vil gentage og hvor du kan replikeres til.

1. Vælg din samling bladet **gendannelse Services vaults** og klik på **Indstillinger**.
2. I **Indstillinger for** > **Kom godt i gang** skal du klikke på **Gendannelse af websteder** > **trin 1: forberede infrastruktur** > **beskyttelse mål**.

    ![Vælg mål](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. Vælg **Til Azure**i **målet for beskyttelse** , og vælg **Ja, med Hyper-V**. Vælg **Nej** for at bekræfte, at du ikke bruger VMM. Klik derefter på **OK**.

    ![Vælg mål](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Trin 2: Konfigurere kildemiljøet

Konfigurere webstedet Hyper-V, installere Azure websted gendannelse udbyder og Azure gendannelse Services agent på Hyper-V værter og registrere værterne i samling af legitimationsoplysninger.


1. Klik på **trin 2: forberede infrastruktur** > **kilde**. For at tilføje et nyt Hyper-V-websted som en beholder for din Hyper-V hosts eller grupper, skal du klikke på **+ Hyper-V websted**.

    ![Konfigurere kilde](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. Skriv et navn for webstedet i bladet **oprette Hyper-V-websted** . Klik derefter på **OK**. Vælg det sted, du lige har oprettet.

    ![Konfigurere kilde](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Klik på **+ Hyper-V Server** for at tilføje en server til webstedet.
4. I **Tilføj Server** > **servertype** kontrollere, at **Hyper-V server** vises. Kontrollér, at den Hyper-V-server, du vil tilføje overholder [forudsætninger](#on-premises-prerequisites) og kan få adgang til de angivne URL-adresser.
4. Hente installationsfilen til Azure websted gendannelse udbyder. Du kører denne fil for at installere både provideren og tjenester til genoprettelse agent på hver Hyper-V-vært.
5. Hent tasten registrering. Du skal bruge dette, når du kører installationsprogrammet. Nøglen er gyldige for 5 dage, når du opretter den.

    ![Konfigurere kilde](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Kør provideren installationsfilen på hver vært, du har føjet til webstedet Hyper-V. Hvis du installerer på en Hyper-V klynge, skal du køre installationsprogrammet på hver klyngenode. Installere og registrere hver Hyper-V klynge noder sikrer, at virtuelle maskiner forbliver beskyttede, selvom de flyttes på tværs af noder.

### <a name="install-the-provider-and-agent"></a>Installere udbyder og agent

1. Kør provideren installationsfilen.
2. I **Microsoft Update** kan du vælge i efter opdateringer, så udbyder opdateringer installeres i overensstemmelse med din Microsoft Update-politik.
3. **Installation** acceptere eller ændre standardplaceringen udbyder og klik på **Installer**.
4. Klik på **Gennemse** for at vælge den samling-fil, du har hentet på siden **Indstillinger for samling af legitimationsoplysninger** . Angiv abonnementets Azure gendannelse af websteder, samling navnet og Hyper-V webstedet som Hyper-V server tilhører.

    ![Registrering af Server](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. Angiv, hvordan den udbyder, der skal installeres på serveren opretter forbindelse til Azure gendannelse af websteder via internettet i **Proxyindstillinger** .

- Vælg **Opret forbindelse direkte uden en proxy**, hvis du vil hos udbyderen for at oprette forbindelse direkte.
- Vælg **Opret forbindelse til eksisterende proxyindstillinger**, hvis du vil oprette forbindelse til proxyen, der er i øjeblikket er konfigureret på serveren.
- Hvis din eksisterende proxy kræver godkendelse, eller du vil bruge en brugerdefineret proxy til udbyder forbindelse Vælg **oprette forbindelse til brugerdefineret proxyindstillinger**.
- Hvis du bruger en brugerdefineret proxy, skal du angive den adresse, port og legitimationsoplysninger
- Hvis du bruger en proxy foretage er sikker URL-adresser, der er beskrevet i [forudsætninger, der](#on-premises-prerequisites) tilladt igennem.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. Klik på **Registrer** for at registrere serveren i samling af legitimationsoplysninger, efter installationen er fuldført.

![Installere placering](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. Når registrering afsluttes metadata fra Hyper-V server er hentet ved Azure gendannelse af websteder og serveren vises på siden **Indstillinger for** > **Websted gendannelse infrastruktur** > **Hyper-V Hosts** blade.


### <a name="command-line-installation"></a>Kommandolinjen installation

Azure websted gendannelse udbyder og agent kan også installeres ved hjælp af følgende kommandolinjen. Denne metode kan bruges til at installere udbyderen på en Server Core til Windows Server 2012 R2.

1. Hente installation fil- og registreringsoplysninger nøglen Provider til en mappe. For eksempel C:\ASR.
2. Kør følgende kommandoer til at uddrage udbyder installer fra en kommandoprompt:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Kør denne kommando for at installere komponenterne:

            C:\ASR> setupdr.exe /i

4. Kør derefter disse kommandoer til at registrere serveren i samling af legitimationsoplysninger: CD C:\Program Files\Microsoft Azure websted gendannelse Provider\
            C:\Program Files\Microsoft Azure websted gendannelse udbyder\> DRConfigurator.exe /r /Friendlyname <friendly name of the server> /legitimationsoplysninger<path of the credentials file>

<br/>
Hvor:

- **/Credentials** : obligatorisk parameter, der angiver den placering, hvor den vigtige registreringsdatabasefil er placeret  
- **/FriendlyName** : obligatorisk parameter for navnet på den server, Hyper-V-vært, der vises i portalen Azure gendannelse af websteder.
- **/proxyAddress** : valgfri parameter, der angiver adressen på proxyserveren.
- **/proxyport** : valgfri parameter, der angiver porten af proxy-server.
- **/proxyUsername** : valgfri parameter, der angiver brugernavnet Proxy (hvis proxy kræver godkendelse).
- **/proxyPassword** : valgfri parameter, der angiver adgangskoden til godkendelse med proxy-server (hvis proxy kræver godkendelse).


## <a name="step-3-set-up-the-target-environment"></a>Trin 3: Konfigurere miljøet mål

Angiv Azure lagerplads kontoen skal bruges til gentagelse og Azure netværket som Azure FOS vil oprette forbindelse efter failover.

1.  Klik på **Forbered infrastruktur** > **mål** og vælg det Azure abonnement, du vil bruge.
2.  Angiv den implementeringsmodel, du vil bruge til FOS efter failover.
3.  Gendannelse af websteder kontrollerer, at du har en eller flere kompatible Azure lagerplads konti og netværk.

    ![Lagerplads](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.  Hvis du ikke har oprettet en lagerplads-konto, og du vil oprette en ved hjælp af Ressourcestyring skal du klikke på **+ lagerplads** konto for at gøre indbygget. Angive et kontonavn, type, abonnement og placering på bladet **Opret lagerplads konto** . Kontoen skal være i samme placering, som samling af legitimationsoplysninger gendannelsestjenester.

    ![Lagerplads](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

    Hvis du vil oprette en lagerplads konto ved hjælp af klassisk modellen, du bruger [i portalen Azure](../storage/storage-create-storage-account-classic-portal.md).

5.  Hvis du ikke har skabt et netværk med Azure, og du vil oprette en ved hjælp af Ressourcestyring klikke på **+ netværk** til at gøre indbygget. Angiv et netværksnavn, adresseområde, undernet detaljer, abonnement og placering på bladet **Opret virtuelt netværk** . Netværket skal være i samme placering, som samling af legitimationsoplysninger gendannelsestjenester.

    ![Netværk](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

    Hvis du vil oprette et netværk med klassisk modellen, du bruger [i portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).


## <a name="step-4-set-up-replication-settings"></a>Trin 4: Konfigurere indstillinger for gentagelse

1. Oprette en ny replikering politik skal du klikke på **Forbered infrastruktur** > **Replikeringsindstillinger** > **+ Opret og Tilknyt**.

    ![Netværk](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. Angiv et navn på politik i **oprette og knytte politik** .
3. Angiv, hvor ofte du vil gentage delta data efter den indledende gentagelse (hver 30 sekunder, 5 eller 15 minutter) i **kopiere frekvens** .
4. I **gendannelse Peg opbevaring**, angive i timer hvor lang tid vinduet opbevaring bliver for hvert gendannelsespunkt. Beskyttet computere kan gendannes til et punkt i et vindue.
6. I **App-ensartet øjebliksbillede frekvens** angive, hvor ofte (1-12 timer) gendannelse punkter, der indeholder programmet ensartet snapshots bliver oprettet. Hyper-V anvender to typer af snapshots – et standard øjebliksbillede, der indeholder en trinvis øjebliksbillede af hele virtuelt og et program-ensartet øjebliksbillede, der tager et punkt-in-time øjebliksbillede af programmet dataene i den virtuelle maskine. Program-ensartet snapshots Brug skygge tjenesten VSS (Volume Copy) til at sikre, at programmer er i en ensartet tilstand, når snapshot er taget. Bemærk, at hvis du aktiverer programmet ensartet snapshots, vil det påvirke ydeevnen for programmer, der kører på kilde virtuelle maskiner. Sørg for, at den værdi, du angiver er mindre end antallet yderligere betroet punkter, du konfigurerer.
3. I **indledende gentagelse starttidspunkt** angive, hvornår du skal starte den indledende gentagelse. Replikering forekommer over din internetbåndbredde, så du kan vælge at planlægge det uden for din optaget timer. Klik derefter på **OK**.

    ![Politikken til replikering](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Når du opretter en ny politik er den automatisk knyttet til webstedet Hyper-V. Klik på **OK**. Du kan knytte en Hyper-V-websted (og FOS i den) til flere politikker for gentagelse i **Indstillinger for** > **Gentagelse** > politikkens navn > **Knytte Hyper-V-websted**.

## <a name="step-5-capacity-planning"></a>Trin 5: Kapacitetsplanlægning

Nu hvor du har dit basic infrastruktur konfigurere du kan synes om kapacitetsplanlægning af og finde ud af, om du har brug for yderligere ressourcer.

Gendannelse af websteder giver en kapacitet planlægger for at hjælpe dig med at tildele de rette ressourcer til kilde-miljø, websted gendannelse komponenter, netværk og opbevaring. Du kan køre planlæggeren i hurtigtilstand for skøn baseret på en gennemsnitlige antal FOS, diske og lagerplads eller i detaljeret tilstand, skal du angive figurer på niveauet for arbejdsbyrde. Før du går i gang skal du:

- Indsamle oplysninger om det gentagelse miljø, herunder FOS, diske per FOS og lager per disk.
- Anslå daglige ændring (transportspand) du har for replikerede data. Du kan bruge [Kapacitet Planner til Hyper-V replika](https://www.microsoft.com/download/details.aspx?id=39057) kan hjælpe dig med at gøre dette.

1.  Klik på **Hent** for at hente værktøjet og derefter køre den. [Læse artiklen](site-recovery-capacity-planner.md) , der følger med værktøjet.
2.  Når du er færdig Vælg **Ja** , **du har kørt kapacitet planlæggeren**?

    ![Planlægning af kapacitet](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Overvejelser i forbindelse med netværksbåndbredde

Du kan bruge værktøjet kapacitet teamplanlægning til at beregne den båndbredde, du skal bruge til gentagelse (indledende gentagelse og derefter delta). Hvis du vil styre mængden af båndbredde til replikering har du et par muligheder:

- **Begrænse båndbredde**: Hyper-V trafik, der kopieres til Azure gennemgår en bestemt Hyper-V-vært. Du kan begrænse båndbredde på host serveren.
- **Tilpasse båndbredde**: Du kan påvirke den båndbredde, der bruges af replikering ved hjælp af et par registreringsdatabasenøgler.

#### <a name="throttle-bandwidth"></a>Begrænse båndbredde

1. Åbne snap-in Microsoft Azure sikkerhedskopi MMC på Hyper-V host serveren. En genvej til Microsoft Azure sikkerhedskopiering er tilgængelige på skrivebordet eller på C:\Program Files\Microsoft Azure gendannelse Services Agent\bin\wabadmin som standard.
2. Klik på **Skift egenskaber**i snap-in.
3. Under fanen **Throttling** Vælg **Aktiver brug af internetbåndbredde (throttling) for sikkerhedskopiering**og angive begrænsninger for arbejde og ikke-arbejde timer. Gyldige områder er fra 512 Kbps til 102 Mbps sekundet.

    ![Begrænse båndbredde](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

Du kan også bruge cmdlet'en [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) til at angive (throttling). Her er et eksempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Angiv OBMachineSetting NoThrottle** angiver, at ingen (throttling) er påkrævet.


#### <a name="influence-network-bandwidth"></a>Påvirke netværksbåndbredde

1. Gå til **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**i registreringsdatabasen.
    - Ændre værdien **UploadThreadsPerVM**for at påvirke den båndbredde trafik på en kopierer disk, eller Opret nøgle, hvis den ikke findes.
    - Hvis du vil påvirke båndbredden til failback trafik fra Azure, at ændre værdien **DownloadThreadsPerVM**.
2. Standardværdien er 4. I en "overprovisioned" netværk, bør disse registreringsdatabasenøgler ændres fra standardværdierne. Maksimum er 32. Overvåge trafik til at optimere værdien.

## <a name="step-6-enable-replication"></a>Trin 6: Aktivér gentagelse

Aktiver nu replikering på følgende måde:

1. Klik på **trin 2: gentage programmet** > **kilde**. Når du har aktiveret replikering for første gang skal du klikke på **+ gentage** i samling af legitimationsoplysninger til at aktivere gentagelse til flere computere.

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. I bladet **kilde** > Vælg Hyper-V-websted. Klik derefter på **OK**.
3. Vælg samling abonnementet og failover model, du vil bruge i Azure (klassisk eller ressource management) efter failover i **mål** .
4. Vælg kontoen lagerplads, du vil bruge. Hvis du vil bruge en anden lagerplads konto end dem, har du kan [oprette en](#set-up-an-azure-storage-account). Klik på **Opret ny**for at oprette et lager konto ved hjælp af Ressourcestyring modellen. Hvis du vil oprette en lagerplads konto ved hjælp af klassisk modellen, du bruger [i portalen Azure](../storage/storage-create-storage-account-classic-portal.md). Klik derefter på **OK**.
5.  Vælg Azure Netværks- og undernet som Azure FOS opretter forbindelse til, når de er spundet op efter failover. Vælg **Konfigurer nu til markerede computere** at anvende indstillingen på alle computere, du vælger for beskyttelse. Vælg **Konfigurer senere** for at markere det Azure netværk per maskine. Hvis du vil bruge et andet netværk fra dem, har du kan [oprette en](#set-up-an-azure-network). Klik på **Opret ny**for at oprette et netværk ved hjælp af Ressourcestyring modellen. Hvis du vil oprette et netværk med klassisk modellen, du bruger [i portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Vælg et undernet, hvis det er relevant. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. I **virtuelle maskiner** > **Vælg virtuelle maskiner** skal du klikke på og Vælg hver maskine, du vil gentage. Du kan kun vælge maskiner, gentagelse kan aktiveres. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. I **Egenskaber for** > **konfigurere egenskaber**, Vælg operativsystemet for de valgte FOS og OS disken. Bekræfte, at navnet Azure VM (Target navn) opfylder [Azure virtuelt krav](site-recovery-best-practices.md#azure-virtual-machine-requirements) og ændre den, hvis det er nødvendigt. Klik derefter på **OK**. Du kan angive yderligere egenskaber, der senere.

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. I **Replikeringsindstillinger** > **konfigurere gentagelse indstillinger**skal du vælge politikken gentagelse, du vil anvende til de beskyttede FOS. Klik derefter på **OK**. Du kan ændre politikken gentagelse i **Indstillinger for** > **Gentagelse politikker** > navn på politik > **Rediger indstillinger**. Ændringer, du anvender der skal bruges til maskiner, der allerede replikering og nye maskiner.

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Du kan følge statussen for **Aktivér beskyttelse** jobbet i **Indstillinger for** > **job** > **job gendannelse af websteder**. Når du har kørt **Færdiggøre beskyttelse** jobbet maskinen er klar til failover.

### <a name="view-and-manage-vm-properties"></a>Få vist og administrere VM egenskaber

Vi anbefaler, at du kontrollerer egenskaber for datakilde maskinen.

1. Klik på **Indstillinger for** > **Beskyttet elementer** > **Replikeres elementer** >, og vælg maskinen.

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. Du kan få vist gentagelse og belastningsjustering oplysninger for VM i **Egenskaber** .

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. I **Beregn og netværk** > **beregne egenskaber** kan du angive Azure VM navn- og destinationswebsteder størrelse. Ændre navnet, der opfylder kravene Azure, hvis det er nødvendigt. Du kan også få vist og redigere oplysninger om target netværk, undernet og IP-adresser, der skal tildeles til Azure VM. Bemærk følgende:

    - Du kan angive target IP-adresse. Hvis du ikke angiver en adresse, den mislykkedes over machine skal bruge DHCP. Hvis du angiver en adresse, der ikke er tilgængelig på failover, mislykkes sekundære. Den samme target IP-adresse kan bruges til test failover, hvis adressen, der er tilgængelige i test failover netværket.
    - Antallet af netværkskort, bestemmes af den størrelse, du angiver for virtuelt mål på følgende måde:

        - Hvis antallet af netværkskort på maskinen kilde er mindre end eller lig med antallet af kort, der er tilladt for target maskine størrelse, derefter får destinationen det samme antal kort som kilde.
        - Hvis antallet af kort til kilde virtual machine overstiger det antal, der er tilladt for mål størrelsen og derefter mål størrelsen maksimum der skal bruges.
        - Eksempelvis hvis en kilde maskine har to netværkskort og mål maskine størrelsen understøtter fire, target maskinen har to kort. Hvis kildecomputer har to kort, men understøttede mål størrelsen understøtter kun en har target maskinen kun ét kort.     
        - Hvis VM har flere netværkskort de kan alle oprette forbindelse til det samme netværk.

    ![Aktivere gentagelse](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.  Du kan se operativsystem og datadisce på VM, der kan replikeres i **diske** .


## <a name="step-7-test-the-deployment"></a>Trin 7: Test installationen

Du kan køre en test sekundær server til en enkelt virtuel computer eller en gendannelse plan, der indeholder en eller flere virtuelle maskiner for at teste installationen.


### <a name="prepare-for-test-failover"></a>Forberede test failover

- Hvis du vil køre en test failover anbefaler vi, at du opretter et nyt Azure netværk, der har identificeret fra netværket Azure fremstilling (dette er standardfunktionsmåden, når du opretter et nyt netværk i Azure). Du kan [Få mere at vide](site-recovery-failover.md#run-a-test-failover) om at køre test failover.
- For at få den bedste ydeevne, når du ikke Azure skal du installere Azure Agent på den beskyttede maskine. Det gør starte hurtigere og hjælper dig med fejlfinding. Installere [Linux](https://github.com/Azure/WALinuxAgent) eller [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) -agent.
- Hvis du vil teste fuldt implementeringen skal du bruge en infrastruktur for replikerede maskinen fungerer som forventet. Du kan oprette en virtuel maskine som et domænenavn fra domænecontrolleren med DNS og replikeres dette til Azure ved hjælp af Azure gendannelse af websteder, hvis du vil teste Active Directory og DNS. Læs mere i [test failover overvejelser i forbindelse med Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
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

- Føje en offentlig IP-adresse til den NIC, der er knyttet til Azure VM tillade RDP.
- Sikre, at du ikke har en hvilken som helst domæne politikker, der forhindrer dig i at oprette forbindelse til en virtuel maskine, ved hjælp af en offentlig adresse.
- Prøv at oprette forbindelse. Hvis du ikke kan oprette forbindelse kan du kontrollere, hvor VM kører. Læs denne [artikel](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)for flere tip til fejlfinding.

Hvis du vil have adgang til en Azure VM kører Linux efter failover ved hjælp af en Secure Shell-klient (ssh), kan du gøre følgende:

**På den lokale computer før failover**:

- Sørg for, at tjenesten Secure Shell på Azure VM er indstillet til at starte automatisk ved systemstart.
- Kontrollér, at firewallregler har givet en SSH forbindelse til den.

**I feltet Azure VM efter failover**:

- Netværk sikkerhed gruppe regler på mislykkedes over VM, og det Azure undernet, som den er tilsluttet skal tillade indgående forbindelser til SSH porten.
- Der skal oprettes et offentlige slutpunkt for at tillade indgående forbindelser på SSH porten (TCP-port 22 som standard).
- Hvis VM er tilgængelige via en VPN-forbindelse (Express distribuere eller VPN-websted til websted) kan klienten bruges til at oprette forbindelse direkte til VM over SSH.

### <a name="run-a-test-failover"></a>Køre en test failover

Sådan køres test failover gør følgende:

1. Mislykkes over en enkelt VM i **Indstillinger for** > **Replikeres elementer**, skal du klikke på VM > **+ Test Failover**.

    ![Test failover](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. Mislykkes over en gendannelse plan i **Indstillinger for** > **Gendannelse plan**, skal du højreklikke på planen > **Test Failover**. Hvis du vil oprette planlægge en genoprettelse [skal du følge disse instruktioner](site-recovery-create-recovery-plans.md).

3. Vælg Azure netværket som Azure FOS forbindes til, når der opstår failover i **Test Failover** .

    ![Test failover](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

4. Klik på **OK** for at starte sekundære. Du kan registrere status for opgaver ved at klikke på VM til at åbne dets properies eller på **Test Failover** jobbet i **Indstillinger for** > **job gendannelse af websteder**.
5. Når sekundære Når fasen **færdigt test** , kan du gøre følgende:
    1. Få vist virtuelt replika i portalen Azure. Kontrollér, at den virtuelle maskine startes korrekt.
    2. Du kan starte en Fjernskrivebord forbindelse til den virtuelle maskine, hvis du er sat op til access virtuelle maskiner fra dit lokale netværk.
    3. Klik på **udført en test** for at afslutte den.
    4. Klik på **noter** for at registrere og gemme de bemærkninger, der er knyttet til sekundære test.
    5. Klik på **sekundære test er fuldført**. Rydde op i testmiljø automatisk sluk og slette virtuelt test.
    6. På dette tidspunkt slettes eventuelle elementer eller VM'er, der er oprettet automatisk af gendannelse af websteder under sekundære test. Du har oprettet for test failover ekstra elementer slettes ikke.

    > [AZURE.NOTE] Hvis en test failover fortsætter mere end to uger, den er fuldført af kraft.

6. Når sekundære er fuldført skal du også kunne se replikaen Azure maskine vises i portalen Azure > **virtuelle computere**. Du skal sikre dig, at VM har den ønskede størrelse, som den er tilsluttet til det pågældende netværk, og at den kører.
7. Hvis du [forberedt for forbindelser efter failover](#prepare-to-connect-to-Azure-VMs-after-failover) du bør kunne oprette forbindelse til Azure VM.


## <a name="failover"></a>Failover

Når første replikering er fuldført for dine maskiner, kan du aktivere failover behovet opstår. Gendannelse af websteder understøtter forskellige typer failover - Test failover, planlagt failover og ikke-planlagt failover.
Du kan [Få mere at vide](site-recovery-failover.md) om forskellige typer af failover og detaljerede beskrivelser af hvornår og hvordan du udfører hver af dem.

> [AZURE.NOTE] Hvis der er meningen til at overføre virtuelle maskiner til Azure, anbefales det, at du bruger en [planlagt Failover handling](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary) til at overføre virtuelle maskiner til Azure. Når programmet overførte er blevet godkendt i Azure ved hjælp af test failover, kan du bruge de trin, der er nævnt under [Komplet migrering](#Complete-migration-of-your-virtual-machines-to-Azure) til at fuldføre overførslen af virtuelle maskiner. Du behøver ikke at udføre en bekræftelse eller Slet. Komplet migrering er fuldført overførslen, fjerner beskyttelse til den virtuelle maskine og stopper Azure gendannelse af websteder fakturering for maskinen.


###<a name="run-an-unplanned-failover"></a>Køre en ikke-planlagt Failover

Dette bør vælges, når et primære websted bliver ikke tilgængelig på grund af en uventet hændelse, som et power afbrydelse eller virus angreb. Denne procedure beskriver, hvordan du kører en "ikke-planlagt sekundær server' til en plan for gendannelse. Du kan også køre sekundære for en enkelt virtuel maskine under fanen virtuelle computere. Inden du starter, Kontroller, at alle de ønskede mislykkes virtuelle-computere har fuldført indledende gentagelse.

1. Vælg **gendannelse plan > recoveryplan_name**.
2. Klik på bladet gendannelse plan **Ikke-planlagt Failover**.
3. Vælg de kilde- og destinationswebsteder placeringer på siden **ikke-planlagt Failover** .
4. Vælg **Luk virtuelle maskiner og synkronisere de nyeste data** til at angive, at gendannelse af websteder skal prøve at lukke de beskyttede virtuelle maskiner og synkronisere dataene, så den nyeste version af dataene ikke bliver gennemført.
5. Efter sekundære er virtuelle maskiner i en disposition afventer tilstand.  Klik på **Anvend** for at bekræfte sekundære.

[Lær mere](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>Komplet migrering af din virtuelle maskiner til Azure


>[AZURE.NOTE] De følgende fremgangsmåde gælder kun, hvis du vil overføre virtuelle maskiner til Azure

1. Udføre planlagte failover som nævnes [her](site-recovery-failover.md)
2. I **Indstillinger > replikeres elementer**, højreklik på den virtuelle maskine, og vælg **Komplet migrering**

    ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

2. Klik på **OK** for at fuldføre overførslen. Du kan registrere status for opgaver, ved at klikke på VM til at åbne dens egenskaber eller ved at bruge den komplette overførselsjob i **Indstillinger > gendannelse af websteder job**.


## <a name="monitor-your-deployment"></a>Overvåge installationen

Her er, hvordan du kan overvåge konfigurationsindstillinger, status og tilstand for implementeringen gendannelse af websteder:

1. Klik på navnet på samling af legitimationsoplysninger for at få adgang til dashboardet **Essentials** . I dette dashboard kan du gendannelse af websteder job, gentagelse status, gendannelse planer, servertilstand og begivenheder.  Du kan tilpasse Essentials for at få vist felter og layout, der er bedst egnede til dig, herunder status for andre vaults sikkerhedskopiering og gendannelse af websteder.

    ![Essentials](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. I feltet **tilstand** kan du overvåge webstedsservere, der oplever problemet, og de hændelser, der er anledning gendannelse af websteder i de seneste 24 timer.
3. Du kan administrere og overvåge gentagelse i kolonnen **Replikeres elementer**, **Gendannelse planer**, og **Websted gendannelse job** felter. Du kan analysere i sager i **Indstillinger for** -> **job** -> **Websted gendannelse job**.
