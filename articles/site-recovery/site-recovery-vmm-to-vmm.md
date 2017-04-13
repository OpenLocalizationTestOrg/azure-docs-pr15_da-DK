<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner i VMM skyer til en sekundær VMM websted ved hjælp af portalen Azure | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du kan installere Azure gendannelse af websteder for at dirigerer gentagelse, failover og gendannelse af Hyper-V FOS i VMM skyer til en sekundær VMM websted ved hjælp af portalen Azure."
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

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Gentage Hyper-V virtuelle maskiner i VMM skyer til en sekundær VMM websted ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmm-to-vmm.md)
- [Klassisk portal](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - ressourcestyring](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Velkommen til gendannelse af Azure websteder! Brug denne artikel, hvis du vil gentage lokale Hyper-V virtuelle maskiner administreres i System Center Virtual Machine Manager (VMM) skyer til en sekundær websted. I denne artikel beskrives, hvordan du konfigurerer gentagelse ved hjælp af Azure gendannelse af websteder i portalen Azure.

> [AZURE.NOTE] Azure har to forskellige [installation modeller](../resource-manager-deployment-model.md) til oprettelse og arbejde med ressourcer: Azure ressourcestyring og klassisk. Azure har også to portaler – Azure klassisk portalen, der understøtter den klassiske implementeringsmodel og Azure portalen med understøttelse af begge installation-modeller.


Azure gendannelse af websteder i portalen Azure indeholder adskillige nye funktioner:

- I Azure kombineres-portalen, Azure sikkerhedskopiering og gendannelse af websteder Azure services til en enkelt gendannelse Services samling, så du kan konfigurere og administrere Forretningskontinuitet og nedbrud (BCDR) fra ét sted. Et samlet dashboard kan du overvåge og administrere handlinger på tværs af dine lokale websteder og Azure offentlige skyen.
- Brugere med Azure abonnementer klargjort med programmet skyen løsning CSP'EN (Provider) kan nu administrere gendannelse af websteder handlinger i portalen Azure.


Når du har læst i denne artikel, kan du sende kommentarer nederst i Disqus kommentarer. Bed tekniske spørgsmål på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Oversigt

Virksomheder har brug for en strategi for BCDR, der bestemmer, hvordan apps, arbejdsmængder og data holde køre og være tilgængelige under planlagte og ikke-planlagt nedetid, og gendanne til normal arbejdsbetingelser så tidligt som muligt. Din strategi for BCDR skal beholde forretningsdata sikre og kan gendannes, og sikre, at arbejdsbelastninger forbliver konstant er tilgængelig, når der udsendes nedbrud.

Gendannelse af websteder er en Azure-tjeneste, der bidrager til din strategi for BCDR ved orchestrating gentagelse af lokale fysiske servere og virtuelle maskiner til skyen (Azure) eller til en sekundær datacenter. Hvis der opstår afbrydelser i din primære placering, du ikke sekundær placeringen for at holde apps og arbejdsbelastninger, som er tilgængelige. Du ikke tilbage til din primære placering, når det vender tilbage til normal handlinger. Se mere i [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

Denne artikel indeholder alle de oplysninger, du vil gentage lokale Hyper-V FOS i VMM skyer til en sekundær VMM websted. Den indeholder en oversigt over arkitekturen, planlægning oplysninger og installation trin til konfiguration af lokale servere, Replikeringsindstillinger og kapacitetsplanlægning af. Du kan aktivere gentagelse på computere, du vil beskytte, og Markér failover fungerer, når du har konfigureret infrastrukturen.

## <a name="business-advantages"></a>Business fordele

- Gendannelse af websteder giver beskyttelse til business arbejdsmængder og programmer, der kører på Hyper-V FOS ved replikering dem til en sekundær Hyper-V-server.
- Gendannelse Services-portalen for at oprette, administrere og overvåge gentagelse, failover og genoprettelse på ét sted.
- Du kan køre nemt køre failover fra din primære lokalt infrastruktur til sekundær og failback (Gendan) fra den sekundære websted til primært.
- Du kan konfigurere gendannelse planer med flere computere, så lagdelte arbejdsbelastninger mislykkes sammen.

## <a name="scenario-architecture"></a>Scenarie arkitektur

Dette er de scenarie komponenter:

- **Primære websted**: I det primære websted, der er et eller flere Hyper-V hostservere, der kører FOS kilde, du vil gentage. Disse primære værtsservere er placeret i en privat VMM-sky.
- **Sekundær**: I det sekundære websted, der er en eller flere Hyper-V hostservere, der kører target FOS, at du gentage de primære FOS. Disse værtsservere er placeret i en privat VMM-sky. Skyen kan være på den primære server (Hvis du kun har en enkelt VMM server) eller på en sekundær VMM-server.
- **Udbyder**: forbindelse med installation af gendannelse af websteder, du Installer Azure websted gendannelse udbyder på VMM servere, og registrere disse servere i en samling af legitimationsoplysninger gendannelsestjenester. Den udbyder, der kører på serveren VMM kommunikerer med gendannelse af websteder over HTTPS 443 at gentage organisering. Datareplikering forekommer mellem primære og sekundære Hyper-V værtsservere. Replikerede data forbliver i lokale websteder og netværk og sendes ikke til Azure. Læs mere om [beskyttelse af personlige oplysninger](#privacy-information-for-site-recovery).

![E2E topologi](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Oversigt over data til beskyttelse af personlige oplysninger

I denne tabel indeholder en oversigt over, hvordan data gemmes i dette scenarie:
****
Handling | **Detaljer** | **Indsamlede data** | **Brug** | **Påkrævet**
--- | --- | --- | --- | ---
**Registrering** | Du kan registrere en VMM server i en samling af legitimationsoplysninger gendannelsestjenester. Hvis du senere vil fjerne registreringen af en server, kan du gøre det ved at slette serveroplysninger fra Azure-portalen. | Når en VMM server er registreret gendannelse af websteder indsamler, processer, og overfører metadata om VMM serveren og navnene på de VMM skyer fundet ved gendannelse af websteder. | Data, der bruges til at identificere og kommunikere med den relevante VMM server og konfigurere indstillinger for relevante VMM skyer. | Denne funktion er påkrævet. Hvis du ikke vil sende disse oplysninger til gendannelse af websteder kan du ikke bruge tjenesten gendannelse af websteder.
**Aktivere gentagelse** | Azure websted gendannelse udbyder er installeret på serveren VMM og er bane vejen for kommunikation med tjenesten gendannelse af websteder. Udbyderen, der er et DLL-bibliotek (Dynamic) vært VMM processen. Når udbyderen, der er installeret, bliver aktiveret funktionen "Datacenter gendannelse" i administratorkonsol VMM. Nye og eksisterende FOS kan aktivere denne funktion til at aktivere beskyttelse til en VM. | Med denne egenskab indstillet sender udbyderen, der navnet og ID'ET VM til gendannelse af websteder.  Replikering er aktiveret af Windows Server 2012 eller Windows Server 2012 R2 Hyper-V replika. Virtuelt dataene replikeres fra én Hyper-V vært til en anden (oftest placeret i et andet "gendannelse" datacenter). | Gendannelse af websteder anvender metadata til at udfylde VM oplysningerne i portalen Azure. | Denne funktion er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil sende disse oplysninger, ikke aktivere FOS gendannelse af websteder beskyttelse. Bemærk, at alle data, der er sendt af provideren til gendannelse af websteder sendes over HTTPS.
**Gendannelse plan** | Gendannelse planer hjælpe dig med at opbygge en plan til organisering af gendannelse datacenteret. Du kan angive den rækkefølge, hvori FOS eller en gruppe af virtuelle maskiner skal startes på webstedet gendannelse. Du kan også angive en hvilken som helst automatiseret scripts skal køre eller alle manuel handling til at blive ført på tidspunktet for gendannelse for hver VM. Failover udløses typisk på niveauet gendannelse plan for koordineret gendannelse. | Gendannelse af websteder indsamler, behandler eller overfører metadataene for gendannelse planen, herunder virtuelt metadata, og metadataene i en hvilken som helst automatiseringsscripts og manuel handling noter. | Metadata, der bruges til at opbygge gendannelse planen i portalen Azure. | Denne funktion er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil sende disse oplysninger til gendannelse af websteder, du ikke oprette gendannelse planer.
**Kort over netværk** | Kort netværksoplysninger fra den primære datacenter til datacenteret gendannelse. Når FOS gendannes på webstedet gendannelse, hjælper netværk tilknytning til at oprette forbindelse til netværket. | Gendannelse af websteder indsamler, behandler eller overfører metadataene i de logiske netværk for hvert websted (primær og datacenter). | Metadata, der bruges til at udfylde netværksindstillinger, så du kan overføre netværksoplysningerne. | Denne funktion er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil sende disse oplysninger til gendannelse af websteder, ikke bruge kort over netværk.
**Failover (planlagt/ikke-planlagt/test)** | Failover skifter FOS fra én VMM rettighedsstyret datacenter til en anden. Handlingen failover udløses manuelt i portalen Azure. | Udbyderen på serveren VMM får besked om hændelsen failover ved gendannelse af websteder og kører en failover handling på værten Hyper-V via VMM grænseflader. Faktisk failover af en VM er fra en Hyper-V vært til en anden og behandlet af Windows Server 2012 eller Windows Server 2012 R2 Hyper-V replika. Når failover er fuldført, sender provideren på VMM serveren i datacenter gendannelse succes oplysninger til gendannelse af websteder. | Gendannelse af websteder bruger de oplysninger, der sendes til at udfylde status for failover handling oplysningerne i portalen Azure. | Denne funktion er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil sende disse oplysninger til gendannelse af websteder, ikke brug sekundære.


## <a name="azure-prerequisites"></a>Azure forudsætninger

Her er, hvad du skal i Azure til at installere dette scenarie:

**Forudsætninger** | **Detaljer**
--- | ---
**Azure**| Du har brug for en [Microsoft Azure](http://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.


## <a name="on-premises-prerequisites"></a>Forudsætninger for lokalt

Her er, hvad du skal i de primære og sekundære lokale websteder til at installere dette scenarie:

**Forudsætninger** | **Detaljer**
--- | ---
**VMM** | Vi anbefaler, at du installerer en VMM server i det primære websted og en VMM server i det sekundære websted.<br/><br/> Du kan også [gentage mellem skyer på en enkelt VMM-server](site-recovery-single-vmm.md). Hvis du vil gøre dette skal du mindst to skyer konfigureret på VMM serveren.<br/><br/> VMM servere, der skal kører mindst System Center 2012 SP1 med de seneste opdateringer.<br/><br/> Hver VMM server have på en eller flere skyer konfigureret og alle skyer skal have Hyper-V kapacitet profilen. <br/><br/>Skyer skal indeholde en eller flere VMM host grupper.<br/><br/>Få flere oplysninger om konfiguration af VMM skyer i [konfigurere VMM skyen struktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), og [Gennemgang: oprette private skyer med System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> VMM servere have adgang til internettet.
**Hyper-V** | Hyper-V-servere, der skal køre mindst Windows Server 2012 med Hyper-V-rollen og har installeret de seneste opdateringer.<br/><br/> En Hyper-V server skal indeholde et eller flere FOS.<br/><br/>  Hyper-V værtsservere skal være placeret i host grupper i den primære og sekundære VMM sky.<br/><br/> Hvis du kører Hyper-V i en klynge på Windows Server 2012 R2 skal du installere [opdatere 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Hvis du kører Hyper-V i en klynge på Windows Server 2012, skal du være opmærksom på, klynge broker ikke er oprettet automatisk, hvis du har en statisk IP-adresse-baseret klynge. Du skal konfigurere klynge broker manuelt. [Du finder flere oplysninger](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Udbyder** | Under installationen af gendannelse af websteder, du har installeret Azure websted gendannelse provideren på VMM servere. Udbyderen kommunikerer med gendannelse af websteder over HTTPS 443 til dirigerer gentagelse. Datareplikering forekommer mellem de primære og sekundære Hyper-V-servere over LAN eller en VPN-forbindelse.<br/><br/> Den udbyder, der kører på serveren VMM skal have adgang til disse URL-adresser: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. BLOB.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Desuden kan firewall kommunikation fra VMM servere til [Azure datacenter IP-intervaller](https://www.microsoft.com/download/confirmation.aspx?id=41653) og tillade HTTPS (443)-protokollen.

## <a name="prepare-for-deployment"></a>Forberede til installation

Sådan forbereder du dig til installation, skal du:

1. [Forberede VMM serveren](#prepare-the-vmm-server) til installation af gendannelse af websteder.
2. [Klargør til kort over netværk](#prepare-for-network-mapping). Konfigurere netværk, så du kan konfigurere kort over netværk.

### <a name="prepare-the-vmm-server"></a>Forberedelse af VMM-serveren

Sørg for, at VMM serveren er i overensstemmelse med [forudsætninger](#on-premises-prerequisites) og kan få adgang til de angivne URL-adresser.


### <a name="prepare-for-network-mapping"></a>Forberede netværk tilknytning

Netværk tilknytning kort mellem VMM VM netværk på de primære og sekundære VMM servere til:

- Placer optimalt replika FOS på sekundær Hyper-V-værter efter failover.
- Forbind replika FOS med relevante VM netværk.
- Hvis du ikke konfigurerer netværk tilknytning replika FOS ikke være forbundet med en hvilken som helst netværk efter failover.
- Hvis du vil konfigurere netværk er tilknytning under gendannelse af websteder installation her, hvad du skal:

    - Sørg for, at FOS på kildeserveren Hyper-V host er tilsluttet et VMM VM netværk. Dette netværk skal være knyttet til en logisk netværk, der er knyttet til skyen.
    - Kontrollere, at den sekundære sky, du bruger til gendannelse har en tilsvarende VM netværk, der er konfigureret. VM netværket skal være knyttet til en logisk netværk, der er knyttet til den sekundære sky.

- Du kan [Få mere at vide](site-recovery-network-mapping.md) om, hvordan netværk tilknytning fungerer.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Forberede til distribution sammen med en enkelt VMM-server

Du kan gentage FOS i Hyper-V hosts i skyen VMM til [Azure](site-recovery-vmm-to-azure.md) eller til en sekundær VMM sky, hvis du kun har en enkelt VMM-server. Vi anbefaler, at den første indstilling fordi replikering mellem skyer ikke er problemfri, men hvis du har brug at gøre dette her er, hvad du skal gøre:

1. **Konfigurere VMM på en Hyper-V VM**. Vi anbefaler du colocate SQL Server-forekomst, der bruges af VMM på den samme VM, når vi gør dette. Dette sparer tid, som kun én VM der skal oprettes. Hvis du vil bruge remote forekomst af SQL Server og en afbrydelse opstår, du vil gendanne forekomsten, før du kan gendanne VMM.
2. **Sørg for, at serveren VMM har mindst to skyer, der er konfigureret**. Én sky skal indeholde FOS du vil gentage og andre skyen skal fungere som den sekundære placering. I skyen, der indeholder de FOS, du vil beskytte skal overholde [forudsætninger](#on-premises-prerequisites).
3. Konfigurere gendannelse af websteder, som beskrevet i denne artikel. Oprette og registrere VMM serveren i samling af legitimationsoplysninger, konfigurere en politik for gentagelse og aktivere gentagelse. Du skal angive den første replikering finder sted via netværket.
4. Når du konfigurerer netværk tilknytning kan du knytte VM netværket for den primære sky VM netværk for den sekundære sky.
5. Aktivere Hyper-V replika på værten Hyper-V, der indeholder den VMM VM i Hyper-V Manager-konsollen, og Aktivér gentagelse på VM. Kontrollér, at du ikke tilføjer virtuelt VMM til sky, der er beskyttet af gendannelse af websteder, til at sikre, at Hyper-V replika indstillinger ikke tilsidesættes af gendannelse af websteder.
6. Hvis du opretter gendannelse-planer til failover kan du bruge den samme VMM server til kilde- og destinationswebsteder.
7. Du mislykkes og gendanne på følgende måde:

    - Manuelt skifte over VMM VM til webstedet sekundær ved hjælp af Hyper-V Manager med en planlagt failover.
    - Mislykkes over FOS.
    - Når den primære VMM VM er blevet gendannet, log på portalen Azure -> gendannelse Services samling og køre en ikke-planlagt failover af FOS fra webstedet sekundær til det primære websted.
    - Når den ikke-planlagte sekundære er fuldført, kan alle ressourcer, der åbnes fra det primære websted igen.


### <a name="create-a-recovery-services-vault"></a>Oprette en samling af legitimationsoplysninger gendannelse

1. Log på [Azure-portalen](https://portal.azure.com).
2. Klik på **nyt** > **Management** > **gendannelsestjenester**. Alternativt kan du klikke på **Gennemse** > **Gendannelse Services** vaults > **Tilføj**.

    ![Ny samling](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. Angiv et navn til at identificere samling af legitimationsoplysninger i **navn** . Hvis du har mere end ét abonnement, skal du vælge en af dem.
4. [Opret en ny ressourcegruppe](../resource-group-template-deploy-portal.md) eller Vælg et eksisterende dokument, og Angiv et Azure område. Computere kan replikeres til dette område. Understøttede områder se for at kontrollere geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Hvis du vil hurtigt få adgang til samling af legitimationsoplysninger fra dashboardet klikke på **Fastgør til dashboard** > **Opret samling**.

    ![Ny samling](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

Den nye samling af legitimationsoplysninger vises på **dashboardet** > **alle de ressourcer**, og primære **gendannelse Services vaults** blade.




## <a name="getting-started"></a>Kom godt i gang

Gendannelse af websteder giver en introduktion oplevelse, der hjælper dig med at installere så hurtigt som muligt. Kom godt i gang kontrollerer forudsætninger og vejleder dig gennem installation trinnene i den rigtige rækkefølge gendannelse af websteder.

Komme i gang du vælge typen af maskiner, du vil gentage, og hvor du kan replikeres til. Du konfigurere lokale servere, oprette replikering politikker og udfører kapacitetsplanlægning. Når du har konfigureret din infrastruktur kan du aktivere gentagelse til FOS. Du kan køre failover for bestemte maskiner, eller Opret gendannelse planer mislykkes over flere computere.

Begynde at komme i gang ved at vælge, hvordan du vil installere gendannelse af websteder. Kom godt i gang strømmen ændres en smule afhængigt af dine krav til gentagelse.

## <a name="step-1-choose-your-protection-goal"></a>Trin 1: Vælg formålet beskyttelse

Vælg, hvad du vil gentage og hvor du kan replikeres til.

1. Vælg din samling bladet **gendannelse Services vaults** og klik på **Indstillinger**.
2. I **Indstillinger for** > **Kom godt i gang** skal du klikke på **Gendannelse af websteder** > **trin 1: forberede infrastruktur** > **beskyttelse mål**.
3. Vælg **til gendannelse websted**i **målet for beskyttelse** , og vælg **Ja, med Hyper-V**.
4. Vælg **Ja** for at vise VMM bruger til at administrere Hyper-V hosts, og vælg **Ja** , hvis du har en sekundær VMM-server. Hvis du implementering af replikering mellem skyer på en enkelt VMM server kan du klikke på **Nej**. Klik derefter på **OK**.

    ![Vælg mål](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>Trin 2: Konfigurere kildemiljøet

Installer Azure websted gendannelse udbyder på VMM servere, og registrere servere i samling af legitimationsoplysninger.

1. Klik på **trin 2: forberede infrastruktur** > **kilde**.

    ![Konfigurere kilde](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. Klik på **+ VMM** for at tilføje en VMM server i **forberede kilde** .

    ![Konfigurere kilde](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. I den **Tilføj Server** blade check, **System Center VMM server** vises i **servertype** og, der opfylder VMM serveren [forudsætninger og URL-adressen krav](#on-premises-prerequisites).
4. Hente installationsfilen til Azure websted gendannelse udbyder.
5. Hent tasten registrering. Du skal bruge denne, når du kører installationsprogrammet. Nøglen er gyldige for 5 dage, når du opretter den.

    ![Konfigurere kilde](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Installer Azure websted gendannelse udbyder på VMM serveren.

> [AZURE.NOTE] Du behøver ikke at installere noget eksplicit på Hyper-V værtsservere.


### <a name="set-up-the-azure-site-recovery-provider"></a>Konfigurere provideren Azure websted gendannelse

1. Kør provideren installationsfilen på hver VMM server. Hvis VMM er implementeret i en klynge, og du installerer provideren for første gang installere det på en aktive node og fuldføre installationen for at registrere VMM serveren i samling af legitimationsoplysninger. Installer udbyderen på de andre noder. Klyngenoder skal alle køre den samme version af provideren.
2. Installationsprogrammet kører et par prerequirement Kontroller og anmoder om tilladelse til at stoppe tjenesten VMM. Tjenesten VMM genstartes automatisk, når installationen er færdig. Hvis du installerer på en VMM klynge, bliver du bedt om at stoppe rollen klynge.

2.  I **Microsoft Update** kan du vælge i efter opdateringer, så udbyder opdateringer installeres i overensstemmelse med din Microsoft Update-politik.
3. **Installation** acceptere eller ændre standardplaceringen udbyder og klik på **Installer**.

    ![Installere placering](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Klik på **Registrer** for at registrere serveren i samling af legitimationsoplysninger, når installationen er fuldført.

    ![Installere placering](./media/site-recovery-vmm-to-vmm/provider-register.png)

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

13.  Klik på **Næste** for at fuldføre processen. Efter registreringen hentes metadata fra serveren VMM ved Azure gendannelse af websteder. Serveren, der vises under fanen **VMM servere** på siden **servere** i samling af legitimationsoplysninger.

    ![Server](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Når serveren er tilgængelig i konsollen gendannelse af websteder i **kilden** > **Forbered kilde** vælge VMM serveren, og vælg værten Hyper-V ligger i skyen. Klik derefter på **OK**.

#### <a name="command-line-installation"></a>Kommandolinjen installation

Azure websted gendannelse udbyder kan installeres fra kommandolinjen. Denne metode kan bruges til at installere udbyderen på Server Core til Windows Server 2012 R2.

1. Hente installation fil- og registreringsoplysninger nøglen Provider til en mappe. For eksempel C:\ASR.
2. Stoppe tjenesten System Center Virtual Machine Manager.
3. Kør følgende kommandoer til at uddrage udbyder installer fra en kommandoprompt:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Kør denne kommando for at installere udbyderen:

        C:\ASR> setupdr.exe /i

5. Kør derefter disse kommandoer til at registrere serveren i samling af legitimationsoplysninger:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Hvor er parametrene:

 - **/Credentials**: obligatorisk parameter, der angiver den placering, hvor den vigtige registreringsdatabasefil er placeret  
 - **/FriendlyName**: obligatorisk parameter for navnet på den server, Hyper-V-vært, der vises i portalen Azure gendannelse af websteder.
 - **/EncryptionEnabled**: valgfri Parameter, som du kun bruge ved replikering fra VMM Azure.
 - **/proxyAddress**: valgfri parameter, der angiver adressen på proxyserveren.
 - **/proxyport**: valgfri parameter, der angiver porten af proxy-server.
 - **/proxyUsername**: valgfri parameter, der angiver brugernavnet Proxy (hvis proxy kræver godkendelse).
 - **/proxyPassword**: valgfri parameter, der angiver adgangskoden til godkendelse med proxy-server (hvis proxy kræver godkendelse).  

## <a name="step-3-set-up-the-target-environment"></a>Trin 3: Konfigurere miljøet mål

Vælg destination VMM server og skyen.

1. Klik på **Forbered infrastruktur** > **mål** og vælg den ønskede VMM server, du vil bruge.
2.  Sky på serveren, der er synkroniseret med gendannelse af websteder vises. Vælg destination skyen.

    ![Mål](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Trin 4: Konfigurere indstillinger for gentagelse

1. Oprette en ny replikering politik skal du klikke på **Forbered infrastruktur** > **Replikeringsindstillinger** > **+ Opret og Tilknyt**.

    ![Netværk](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. Angiv et navn på politik i **oprette og knytte politik** . Kilde- og destinationswebsteder typen skal være **Hyper-V**.
3. Vælg, hvilket operativsystem der kører på værten i **Hyper-V host version** .

    > [AZURE.NOTE] VMM skyen kan indeholde Hyper-V hosts kører forskellige (understøttede) versioner af Windows Server, men en politik for gentagelse er anvendt hosts kører samme-operativsystemet. Hvis du har værter, der kører mere end én operativsystemversion derefter oprette separate gentagelse politikker.

4. Angiv, hvordan trafik skal godkendes mellem primær og gendannelse Hyper-V host-servere i **godkendelsestype** og **godkendelse port** . Vælg **certifikat** , medmindre du har en arbejde Kerberos-miljø. Certifikater til HTTPS-godkendelse konfigureres automatisk af Azure gendannelse af websteder. Du behøver ikke at gøre noget manuelt. Som standard åbnes port 8083 og 8084 (for certifikater) i Windows Firewall på værtsservere Hyper-V. Hvis du markerer **Kerberos**, bruges en Kerberos brugertilladelse til fælles godkendelse af host-serverne. Bemærk, at denne indstilling er kun relevant for Hyper-V hostservere, der kører på Windows Server 2012 R2.
3. Angiv, hvor ofte du vil gentage delta data efter den indledende gentagelse (hver 30 sekunder, 5 eller 15 minutter) i **kopiere frekvens** .
4. I **gendannelse Peg opbevaring**, angive i timer hvor lang tid vinduet opbevaring bliver for hvert gendannelsespunkt. Beskyttet computere kan gendannes til et punkt i et vindue.
6. I **App-ensartet øjebliksbillede frekvens** angive, hvor ofte (1-12 timer) gendannelse punkter, der indeholder programmet ensartet snapshots bliver oprettet. Hyper-V anvender to typer af snapshots – et standard øjebliksbillede, der indeholder en trinvis øjebliksbillede af hele virtuelt og et program-ensartet øjebliksbillede, der tager et punkt-in-time øjebliksbillede af programmet dataene i den virtuelle maskine. Program-ensartet snapshots Brug skygge tjenesten VSS (Volume Copy) til at sikre, at programmer er i en ensartet tilstand, når snapshot er taget. Bemærk, at hvis du aktiverer programmet ensartet snapshots, vil det påvirke ydeevnen for programmer, der kører på kilde virtuelle maskiner. Sørg for, at den værdi, du angiver er mindre end antallet yderligere betroet punkter, du konfigurerer.
7. Angiv, om der skal komprimeres replikerede data, der er overført i **dataoverførsel komprimering** .
8. Vælg **Slet replika VM** til at angive, at den replika virtuelle maskine skal slettes, hvis du deaktiverer beskyttelse for kilden VM. Hvis du aktiverer denne indstilling, når du deaktiverer beskyttelse for kilden VM er den fjernet fra konsollen gendannelse af websteder, indstillinger for gendannelse af websteder for VMM fjernes fra konsollen VMM, og replikaen slettes.
3. I **indledende gentagelse metode** Hvis du replikering via netværket angive, om skal starte den første replikering eller Planlæg, hvornår den. Hvis du vil gemme netværksbåndbredde vil du planlægge uden for din optaget timer. Klik derefter på **OK**.

    ![Politikken til replikering](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Når du opretter en ny politik er den automatisk knyttet til VMM skyen. Klik på **OK**i **politikken til replikering** . Du kan knytte yderligere VMM skyer (og FOS i dem) til denne politik skal skrives gentagelse i **Indstillinger for** > **Gentagelse** > navn på politik > **Knytte VMM skyen**.

    ![Politikken til replikering](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Forberede offline indledende gentagelse

Du kan gøre offline gentagelse til den oprindelige data kopi. Du kan forberede dette på følgende måde:

- På kildeserveren, skal du angive stien til placeringen hvorfra dataeksporten skal foregå. Tildele fuld kontrol NTFS og dele tilladelser til tjenesten VMM på den eksporterede sti. På serveren, skal du angive stien til placeringen hvorfra dataimporten skal forekomme. Tildele de samme tilladelser på denne importsti.
- Hvis import eller eksport stien er delt, skal du tildele Administrator, Superbrugere, Udskriv Operator eller Server Operator gruppemedlemskab til VMM service-kontoen på fjerncomputeren, hvor den delte er placeret.
- Hvis du bruger en hvilken som helst Kør som konti til at tilføje hosts, om import og eksport af stier, tildele læse- og skrivetilladelser til Kør som kontiene i VMM.
- Import og eksport af aktier findes ikke på en hvilken som helst computer, der bruges som Hyper-V host server, fordi tilbagekobling konfiguration ikke understøttes af Hyper-V.
- I Active Directory, på hver Hyper-V begrænset host server, der indeholder virtuelle maskiner, du vil beskytte, aktivere og konfigurere delegering at have tillid til de eksterne computere, som import og eksport af stierne er placeret, som følger:
    1. Åbn på domænecontrolleren, **Active Directory-brugere og computere**.
    2. Klik på **domænenavn**i konsoltræet > **computere**.
    3. Højreklik på servernavnet Hyper-V host > **Egenskaber**.
    4. Klik på **Hav tillid til denne computer til delegering til angivne services**under fanen **delegering** .
    5. Klik på **Brug en hvilken som helst godkendelse protocol**.
    6. Klik på **Tilføj** > **brugere og computere**.
    7. Skriv navnet på den computer, hvor den eksporterede sti > **OK**. På listen over tilgængelige tjenester, holde CTRL-tasten nede og klikke på **cifs** > **OK**. Gentag for navnet på den computer, der er vært Importér stien. Gentag efter behov for yderligere Hyper-V værtsservere.


### <a name="configure-network-mapping"></a>Konfigurer tilknytning af netværk

Konfigurere netværk tilknytning mellem kilde- og destinationswebsteder netværk.

- [Læse](#prepare-for-network-mapping) en hurtig oversigt over tilknytning hvilke netværk understøtter. I addition [læse](site-recovery-network-mapping.md) til en mere detaljeret beskrivelse.
- Kontrollér, at virtuelle maskiner på VMM servere er tilsluttet et VM netværk.

Konfigurere tilknytning på følgende måde:

1. **Indstillinger for** > **Websted gendannelse infrastruktur** > **Netværk tilknytning** > **netværk tilknytninger** skal du klikke på **+ netværk tilknytning**.

    ![Kort over netværk](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. Markér kilden under fanen **Tilføj netværk tilknytning** og målrette VMM servere. De VM netværk, der er knyttet til VMM serverne skal hentes.
3. Vælg det netværk, du vil bruge på listen over VM netværk, der er knyttet til den primære VMM server i **kilde netværk**.
6. Vælg det netværk, du vil bruge på den sekundære VMM server i **Target netværk** . Klik derefter på **OK**.

    ![Kort over netværk](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Her er, hvad der sker, når netværk tilknytning begynder:

- En hvilken som helst eksisterende replika virtuelle maskiner, der svarer til kilde VM netværket forbindes til destinationen VM netværket.
- Nye virtuelle maskiner, der er forbundet til kilde VM netværket forbindes til, der er tilknyttet destinationsnetværket efter gentagelse.
- Hvis du redigerer en eksisterende tilknytning med et nyt netværk, forbindes replika virtuelle maskiner ved hjælp af de nye indstillinger.
- Hvis target netværket har flere undernet og én af disse undernet har samme navn som undernet, hvor virtuelt kilde er placeret, der derefter virtuelt replika skal knyttes til pågældende target undernet efter failover. Hvis der er ingen target undernet med et tilsvarende navn skal forbindes den virtuelle maskine til det første undernet i netværket.

### <a name="configure-storage-mapping"></a>Konfigurer tilknytning af lager
Når du gentage en virtuel maskine på en kilde Hyper-V host-server til en destination Hyper-V host-server, gemmes replikerede data som standard på standardplaceringen, der er angivet for den destinationsadresse Hyper-V vært i Hyper-V Manager. Få mere kontrol over hvor replikerede data er gemt, kan du konfigurere lagerplads tilknytning<br/><br/> For at konfigurere tilknytning af lagerplads, skal du konfigurere lagerplads klassificeringer på kilde- og målrette VMM servere, inden du starter installation. I øjeblikket understøttes lagerplads tilknytning via nye Azure portal ikke. Det kan dog være aktiveret via Powershell. [Få mere at vide](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Trin 5: Kapacitetsplanlægning

Nu hvor du har dit basic infrastruktur konfigurere du kan synes om kapacitetsplanlægning af og finde ud af, om du har brug for yderligere ressourcer.

Gendannelse af websteder indeholder en Excel-baserede kapacitet teamplanlægning for at hjælpe dig med at tildele de rette ressourcer til kilde-miljø, websted gendannelse komponenter, netværk og opbevaring. Du kan køre planlæggeren i hurtigtilstand for skøn baseret på en gennemsnitlige antal FOS, diske og lagerplads eller i detaljeret tilstand, skal du angive figurer på niveauet for arbejdsbyrde. Før du går i gang skal du:

- Indsamle oplysninger om det gentagelse miljø, herunder FOS, diske per FOS og lager per disk.
- Anslå daglig ændring (transportspand) du har til replikerede delta data. Du kan bruge [kapacitet planner til Hyper-V replika](https://www.microsoft.com/download/details.aspx?id=39057) kan hjælpe dig med at gøre dette.

1.  Klik på **Hent** for at hente værktøjet og derefter køre den. [Læse artiklen](site-recovery-capacity-planner.md) , der følger med værktøjet.
2.  Når du er færdig Vælg **Ja** , **du har kørt kapacitet planlæggeren**?

    ![Planlægning af kapacitet](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Kontrolelementet båndbredde

Når du har indsamlet realtid delta gentagelse oplysninger ved hjælp af kapacitet Planner Hyper-V replika, Excel-baserede kapacitet planner værktøjet hjælper dig med at beregne den båndbredde, du skal bruge til gentagelse (indledende og delta). Du kan konfigurere politikken NetQos ved hjælp af gruppepolitik eller Windows PowerShell for at styre mængden af båndbredde, der bruges til gentagelse. Der er nogle måder, kan du gøre dette:

**PowerShell** | **Detaljer**
--- | ---
**Ny - NetQosPolicy "QoS til destination undernet"** | Begrænse trafik fra en Hyper-V-vært, der kører Windows Server 2012 R2 til en sekundær undernet. Brug, hvis din primære og sekundære undernet er forskellige.
**Ny - NetQosPolicy "QoS til destinationsport"** | Begrænse trafik fra en Hyper-V-vært, der kører Windows Server 2012 R2 til en destinationsport.
**Ny - NetQosPolicy "Begrænsning trafik fra VMM"** | Begrænse trafik fra vmms.exe. Dette sænker Hyper-V trafik og Live overførsel trafik. Du kan holde IP-protokoller og porte at afgrænse.

Du kan bruge båndbredde vægt indstillinger eller begrænse trafik med bit per sekundær. Hvis du bruger en klynge, skal du gøre dette på alle klyngenode. Flere oplysninger under:


- Thomas Maurer blog på [(Throttling) Hyper-V replika trafik](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Yderligere oplysninger om [Ny NetQosPolicy cmdlet](https://technet.microsoft.com/library/hh967468.aspx).


## <a name="step-6-enable-replication"></a>Trin 6: Aktivér gentagelse

Aktiver nu replikering på følgende måde:

1. Klik på **trin 2: gentage programmet** > **kilde**. Når du har aktiveret replikering for første gang, du klikke på **+ gentage** i samling af legitimationsoplysninger til at aktivere replikering for flere computere.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. I bladet **kilde** > Vælg VMM server og i skyen, hvor du vil gentage Hyper-V værter er placeret. Klik derefter på **OK**.

    ![Aktivere gentagelse](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. Kontroller i bladet **Target** sekundær VMM server og skyen.
4. Vælg de FOS, du vil beskytte på listen i **virtuelle computere** .

    ![Aktiver virtuelt beskyttelse](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Du kan følge statussen for handlingen **Aktivér beskyttelse** i Indstillinger > **job** > **job gendannelse af websteder**. Når du har kørt **Færdiggøre beskyttelse** jobbet er den virtuelle maskine klar til failover.


>[AZURE.NOTE] Du kan også aktivere beskyttelse af virtuelle maskiner i konsollen VMM. Klik på **Aktivér beskyttelse** på værktøjslinjen i egenskaberne for virtuelt > fanen **Azure gendannelse af websteder** .

Når du har aktiveret replikering kan du få vist egenskaber for VM i **Indstillinger for** > **Replikeres elementer** > vm-navn. Du kan se oplysninger om politikken replikering for VM og dens status på dashboardet til **Essentials** . Klik på **Egenskaber** for at få flere oplysninger.

### <a name="onboard-existing-virtual-machines"></a>Indbyggede eksisterende virtuelle maskiner

Hvis du har eksisterende virtuelle maskiner i VMM, der replikering med Hyper-V replika kan du om bord dem til Azure gendannelse af websteder beskyttelse på følgende måde:

1. Kontrollér, at den Hyper-V-server, der er vært for den eksisterende VM er placeret i den primære sky og, den Hyper-V server, der hoster virtuelt replika er placeret i skyen for sekundær.
2. Sørg for en politik for gentagelse er konfigureret for den primære VMM sky.
2. Aktiver replikering for den primære virtuelle maskine. Azure gendannelse af websteder og VMM sikrer, at den samme replika vært og virtuelt registreres, og Azure gendannelse af websteder skal genbruge og genoprette gentagelse ved hjælp af de angivne indstillinger.


## <a name="step-7-test-your-deployment"></a>Trin 7: Teste din installation

For at teste din installation kan du køre en test sekundær server til en enkelt virtuel maskine, eller Opret en gendannelse plan, der indeholder en eller flere virtuelle computere.

### <a name="prepare-for-failover"></a>Forberede failover

- For at fuldt teste din installation, du har brug for en infrastruktur for replikerede maskinen fungerer som forventet. Du kan oprette en virtuel maskine som et domænenavn fra domænecontrolleren med DNS og replikeres dette til Azure ved hjælp af Azure gendannelse af websteder, hvis du vil teste Active Directory og DNS. Læs mere i [test failover overvejelser i forbindelse med Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Vejledningen i denne artikel beskriver, hvordan du kører en test failover med ingen netværk. Denne indstilling, vil teste, hvor VM skifter, men ikke teste netværksindstillinger for VM. Du kan [Få mere at vide](site-recovery-failover.md#run-a-test-failover) om andre indstillinger.
- Hvis du vil køre en ikke-planlagt failover i stedet for en test failover opmærksom på følgende:

    - Hvis det er muligt skal du lukke primære maskiner før du kører en ikke-planlagt failover. Dette sikrer, at du ikke har både kilde- og replika maskiner, der kører på samme tid.
    - Når du kører en ikke-planlagt failover stopper replikering af data fra primære maskiner, så alle data delta ikke overføres, når en ikke-planlagt failover begynder. Desuden Hvis du kører en ikke-planlagt failover på en plan for gendannelse den kører, indtil fuldført, selvom der opstår en fejl.




### <a name="run-a-test-failover"></a>Køre en test failover

1. Mislykkes over en enkelt VM i **Indstillinger for** > **Replikeres elementer**, skal du klikke på VM > **+ Test Failover**.

    ![Test failover](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Mislykkes over en gendannelse plan i **Indstillinger for** > **Gendannelse plan**, skal du højreklikke på planen > **Test Failover**. Hvis du vil oprette planlægge en genoprettelse [skal du følge disse instruktioner](site-recovery-create-recovery-plans.md).
2. Vælg **ingen** **Test Failover**. Du kan teste, hvor VM skifter som forventet, men den replikerede VM ikke være tilsluttet et netværk med denne indstilling.

    ![Test failover](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Klik på **OK** for at starte sekundære. Du kan registrere status for opgaver ved at klikke på VM til at åbne dens egenskaber eller på **Test Failover** jobbet i **Indstillinger for** > **job** > **job gendannelse af websteder**.
4. Når kørslen failover Når fasen **færdigt test** , kan du gøre følgende:

    -  Få vist replikaen VM i sekundær VMM skyen.
    -  Klik på **fuldført testen** at afslutte op sekundære test.
    -  Klik på **noter** for at registrere og gemme de bemærkninger, der er knyttet til sekundære test.

5. Virtuelt test oprettes på samme vært som vært, som den replika virtuelle maskine findes. Det er føjet til samme skyen, virtuelt replika er placeret.
6. Når du har kontrolleret, der starter FOS korrekt skal du klikke på **sekundære test er fuldført**. På dette tidspunkt, slettes alle elementer, der er oprettet automatisk af gendannelse af websteder under sekundære test.  

    > [AZURE.NOTE] Hvis en test failover fortsætter mere end to uger, den er fuldført af kraft.



### <a name="update-dns-with-the-replica-vm-ip-address"></a>Opdatere DNS med replika VM IP address

Når failover replika VM ikke har muligvis den samme IP-adresse som den primære virtuelle maskine.

- Virtuelle maskiner opdaterer DNS-serveren, de bruger, når de starter.
- Du kan også manuelt opdatere DNS på følgende måde:

#### <a name="retrieve-the-ip-address"></a>Hente IP-adresse

Køre dette eksempelscript for at hente IP-adressen.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Opdatere DNS

Køre dette eksempelscript for at opdatere DNS, der angiver den IP-adresse, du har hentet ved hjælp af det forrige eksempelscript.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Næste trin

Når installationen er konfigureret og kører, kan du [Få mere at vide](site-recovery-failover.md) om forskellige typer failover.
