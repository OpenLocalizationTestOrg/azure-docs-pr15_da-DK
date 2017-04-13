<properties
    pageTitle="Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring | Microsoft Azure"
    description="I denne artikel indeholder en gennemgang af overførslen platform understøttes af ressourcer fra klassisk til Azure ressourcestyring"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring

I denne artikel beskriver vi hvordan vi så overførsel af infrastruktur som en tjeneste (IaaS) ressourcer fra klassiske til Ressourcestyring installation modeller. Du kan læse mere om [Azure ressourcestyring funktioner og fordele](../azure-resource-manager/resource-group-overview.md). Vi viser oplysninger om hvordan du opretter forbindelse ressourcer fra de to installation modeller, der findes på dit abonnement ved hjælp af virtuelt netværk til websted gateways. 

## <a name="goal-for-migration"></a>Mål for overførsel

Ressourceleder, der gør det muligt for implementering af komplekse programmer via skabeloner, konfigurerer virtuelle maskiner ved hjælp af VM filtypenavne og inkorporerer access administration og mærkning. Azure ressourcestyring omfatter SVG og parallelle implementering for virtuelle maskiner i tilgængelighed sæt. Den nye implementeringsmodel giver også Livscyklusadministration af Beregn-, Netværks- og lagerplads på uafhængigt af hinanden. Til sidst, der er fokus på aktivere sikkerhed som standard med håndhævelsen af virtuelle maskiner i et virtuelt netværk.

Næsten alle funktionerne fra den klassiske implementeringsmodel understøttes for Beregn-, Netværks- og lagerplads under Azure ressourcestyring. For at drage fordel af de nye funktioner i Azure Ressourcestyring, kan du overføre eksisterende installationer fra implementeringsmodel klassisk.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Ændringer i dine automatisering og værktøj efter overførslen

Som en del af overføre dine ressourcer fra implementeringsmodel klassisk til implementeringsmodel Ressourcestyring, er det nødvendigt at opdatere din eksisterende automatisering eller et værktøj til at sikre, at det fortsat virker efter overførslen.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Betydningen af overførsel af IaaS ressourcer fra klassisk til Ressourcestyring

Før vi dykke ned i oplysningerne, Lad os se på forskellen mellem data-plan og management-plan handlinger på IaaS ressourcer.

- *Administration af plan* beskrives de opkald, der kommer ind i management-planet eller API til ændring af ressourcer. For eksempel administrere handlinger som oprettelse af en VM, ved at genstarte en VM og opdatering af et virtuelt netværk med et nyt undernet de ressourcer, der kører. De påvirker direkte ikke oprette forbindelse til forekomster.
- *Dataplan* (program) i denne artikel beskrives runtime af programmet selv og involverer interaktion med forekomster, der ikke går igennem Azure API. Få adgang til dit websted eller udtrække data fra en, der kører SQL Server-forekomst eller en MongoDB server skal betragtes som data plan eller program interaktion. Kopiere en blob fra en lagerplads konto og få adgang til en offentlig IP-adresse til RDP eller SSH til den virtuelle maskine er også dataplan. Disse handlinger beholde det program, der kører på tværs af Beregn, netværk og lagerplads.

>[AZURE.NOTE] I visse overførselsscenarier med Azure-platformen stopper, deallocates og genstarter din virtuelle computere. Dette medfører en kort nedetid data-plan.

## <a name="supported-scopes-of-migration"></a>Understøttede områder af overførsel

Der findes tre overførsel områder, der primært fokuserer på Beregn-, Netværks- og lagerplads. 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Overførsel af virtuelle maskiner (ikke i et virtuelt netværk)

Sikkerhed gennemtvinges i implementeringsmodel ressourcestyring til dine programmer som standard. Alle FOS skal være i et virtuelt netværk i ressourcestyring modellen. Azure-platformen genstarter (`Stop`, `Deallocate`, og `Start`) FOS som en del af overførslen. Har du to muligheder for de virtuelle netværk:

- Du kan anmode om platformen til at oprette et nyt virtuelt netværk og overføre den virtuelle maskine til det nye virtuelle netværk.
- Du kan overføre den virtuelle maskine til et eksisterende virtuelt netværk i ressourcestyring.

>[AZURE.NOTE] I denne overførsel omfang, kan både handlingerne management-plan og handlingerne data-plan ikke tillades for et bestemt tidsrum under overførslen.

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Overførsel af virtuelle maskiner (i et virtuelt netværk)

For de fleste VM konfigurationer overføre kun metadata mellem klassisk og ressourcestyring installation modeller. De underliggende FOS kører på den samme hardware, i det samme netværk og med den samme lagerplads. Handlingerne management-plan kan ikke tilladt for en bestemt tidsperiode under overførslen. Dog stadig dataplan til at arbejde. Dine programmer kører oven på FOS (klassisk) det vil sige, ikke betale nedetid under overførslen.

Følgende konfigurationer understøttes ikke i øjeblikket. Hvis support er tilføjet i fremtiden vil nogle FOS i denne konfiguration kan blive opkrævet nedetid (gå gennem stop, deallokere, og genstart VM handlinger).

-   Du har mere end én tilgængelighed, der er angivet i en enkelt skybaseret tjeneste.
-   Du har en eller flere tilgængelighed sæt og VM'er, der ikke er i en tilgængelighed, der er angivet i en enkelt skybaseret tjeneste.

>[AZURE.NOTE] I denne overførsel omfang, kan management planet ikke tillades for et bestemt tidsrum under overførslen. For visse konfigurationer som beskrevet tidligere, data-plan nedetid opstår.

### <a name="storage-accounts-migration"></a>Lagerplads konti overførsel

Hvis du vil tillade problemfri migrering, kan du installere ressourcestyring FOS med en konto med klassisk lagerplads. Med denne egenskab, Beregn og netværk ressourcer kan og skal overføres uafhængigt af lagerplads konti. Når du overfører over dit virtuelle maskiner og virtuelt netværk, skal du overføre over kontiene lagerplads til at fuldføre overførselsprocessen. 

>[AZURE.NOTE] Ressourcestyring implementeringsmodel har ikke begrebet klassisk billeder og diske. Når kontoen lagerplads er overført, klassisk billeder og diske kan ikke ses i ressourcestyring stablen men sikkerhedskopiering virtuelle harddiske forbliver i kontoen lagerplads. 

## <a name="unsupported-features-and-configurations"></a>Ikke-understøttede funktioner og konfigurationer

Vi understøtter aktuelt ikke visse funktioner og konfigurationer. I følgende afsnit beskrives vores anbefalinger dem.

### <a name="unsupported-features"></a>Ikke-understøttede funktioner

Følgende funktioner understøttes ikke i øjeblikket. Du kan eventuelt fjerne disse indstillinger, overføre FOS og genaktivere indstillingerne i implementeringsmodel ressourcestyring.

Ressource-udbyder | Funktion
---------- | ------------
Beregne | Ikke-tilknyttede virtuelt diske.
Beregne | Virtuelt billeder.
Netværk | Slutpunkt ACLs.
Netværk | Virtuelt netværk gateways (websted til websted Azure ExpressRoute, programmet gatewayen, peg på websted).
Netværk | Virtuelle netværk ved hjælp af VNet Peering. (Overføre VNet til ARM og derefter peer) Få flere oplysninger om [VNet Peering] (… /Virtual-Network/Virtual-Network-peering-Overview.MD).
Netværk | Trafik Manager profiler.

### <a name="unsupported-configurations"></a>Ikke-understøttede konfigurationer

Følgende konfigurationer understøttes ikke i øjeblikket.

Tjenesten | Konfiguration | Anbefaling
---------- | ------------ | ------------
Ressourcestyring | Rolle baseret Access kontrolelement (RBAC) for klassisk Ressourcer | Da URI for ressourcerne, der er ændret efter overførslen, anbefales det, at du regner RBAC politik-opdateringer, der skal ske efter overførslen.
Beregne | Flere undernet, der er knyttet til en VM | Opdater undernet konfigurationen til at referere til kun undernet.
Beregne | Virtuelle maskiner, der hører til et virtuelt netværk, men ikke har en eksplicit undernet, der er tildelt | Du kan også kan du slette VM.
Beregne | Virtuelle maskiner, der har beskeder, autoskalering politikker | Overførslen går igennem, og disse indstillinger udelades. Det anbefales, at du evaluerer dit miljø, før du gør overførslen. Du kan alternativt omkonfigurere beskedindstillingerne, når overførslen er fuldført.
Beregne | XML VM extensions (BGInfo 1.*, Visual Studio-fejlfindingen, Web implementere og ekstern fejlfinding) | Dette understøttes ikke. Det anbefales, at du fjerner disse extensions fra den virtuelle maskine at fortsætte overførslen, eller de fjernes automatisk under overflytningen.
Beregne | Start diagnosticering med Premium lagerplads | Deaktivere Start diagnosticering funktion for FOS før du fortsætter med migrering. Du kan aktivere Start diagnosticering ressourcestyring stablede igen, når overførslen er fuldført. Desuden slettes blob, der bruges til skærmbillede og seriel hændelseslogge, så du ikke længere er betale for disse BLOB.
Beregne | Skytjenester, der indeholder web/arbejder roller | Dette understøttes ikke i øjeblikket.
Netværk | Virtuelle netværk, der indeholder virtuelle maskiner og web/arbejder roller |  Dette understøttes ikke i øjeblikket.
Azure App Service | Virtuelle netværk, der indeholder App Service miljøer | Dette understøttes ikke i øjeblikket.
Azure HDInsight | Virtuelle netværk, der indeholder HDInsight tjenester | Dette understøttes ikke i øjeblikket.
Microsoft Dynamics livscyklus Services | Virtuelle netværk, der indeholder virtuelle maskiner, der administreres af Dynamics livscyklus Services | Dette understøttes ikke i øjeblikket.
Beregne | Azure Sikkerhedscenter filtypenavne med en VNET, der indeholder en VPN-gateway eller ER gateway med lokalt DNS-server | Azure Sikkerhedscenter installerer automatisk filtypenavne på din virtuelle maskiner til at overvåge deres sikkerhed og hæve beskeder. Disse udvidelser få normalt installeres automatisk, hvis politikken Azure Sikkerhedscenter er aktiveret på abonnementet. Efterhånden som gateway overførsel understøttes ikke i øjeblikket og gatewayen skal slettes, før du fortsætter med gemmer overførslen, er internetadgang til VM lagerplads konto går tabt, når gatewayen er blevet slettet. Overflytningen kan ikke fortsætte, når dette sker som gæst agent status blob ikke kan udfyldes. Det anbefales at deaktivere Azure Sikkerhedscenter politik på abonnementet 3 timer, inden du fortsætter med migrering.

## <a name="the-migration-experience"></a>Overførsel-oplevelse

Før du begynder overførslen oplevelse, anbefales følgende:

- Sørg for, at de ressourcer, som du vil overføre ikke bruger en ikke-understøttede funktioner eller konfigurationer. Normalt platformen registrerer disse problemer og genererer en fejl.
- Hvis du har VM'er, der ikke er i et virtuelt netværk, vil de ikke længere og deallokeres som en del af Forberedelseshandlingen. Hvis du ikke vil miste den offentlige IP-adresse, kan du se til at reservere IP-adressen før udløser Forberedelseshandlingen. Men hvis FOS er i et virtuelt netværk, de er ikke stoppes og deallokeres.
- Planlægge din migrering under ikke-arbejdstid til for at finde en uventet fejl, der kan udføres under overflytningen.
- Du kan hente den aktuelle konfiguration af din FOS ved hjælp af PowerShell, kommandolinjen (CLI) kommandoer eller REST API'er for at gøre det nemmere for datavalidering, når Forbered trinnet er fuldført.
- Opdater dine automatisering/operationalization scripts for at håndtere implementeringsmodel Ressourcestyring, før du starter overførslen. Du kan også vælge at gøre få handlinger, når ressourcerne, der er i tilstanden forberedt.
- Evaluere de RBAC politikker, der er konfigureret på klassisk IaaS ressourcer og forudse, når overførslen er fuldført.

Overførsel arbejdsprocessen er som følger

![Skærmbillede, der viser arbejdsprocessen for overførsel](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Alle de handlinger, der er beskrevet i følgende afsnit er idempotent. Hvis du har et problem end ikke-understøttede funktioner eller en konfigurationsfejl, anbefales det, at du prøv igen på Forbered afbrydelse eller bekræftelse handling. Azure-platformen forsøger handlingen igen.

### <a name="validate"></a>Validere

Handlingen Valider er det første trin i overførselsprocessen. Formålet med dette trin er at analysere data i baggrunden for ressourcerne, under skifte og vende tilbage succes/fejl, hvis ressourcerne, der er i stand til overførsel.

Du vælger det virtuelle netværk eller tjenesten hostet (hvis det ikke er et virtuelt netværk), du vil validere for overførsel.

* Hvis ressourcen ikke kan skifte, viser Azure-platformen alle årsager til hvorfor det ikke understøttes for overførsel.

### <a name="prepare"></a>Forberede

Forberedelseshandlingen er det andet trin i overførselsprocessen. Formålet med dette trin er at simulere transformation af IaaS ressourcer fra klassisk til Ressourcestyring ressourcer og præsentere dette side om side for dig at visualisere.

Du vælger det virtuelle netværk eller tjenesten hostet (hvis det ikke er et virtuelt netværk), som du vil forberede migrering.

* Hvis ressourcen ikke kan skifte, Azure-platformen stopper overførselsprocessen og viser en liste over årsagen til hvorfor Forberedelseshandlingen mislykkedes.
* Hvis ressourcen kan migrering, langs de første Azure-platformen låse handlingerne management-plan for ressourcer under overførsel. For eksempel er du ikke kan tilføje en datadisk til en VM under overførsel.

Azure-platformen starter derefter overførslen af metadata fra klassisk til Ressourcestyring for de ressourcer, overføre.

Når Forberedelseshandlingen er fuldført, har du mulighed for at visualisere ressourcer i begge klassisk og ressourcestyring. For hver skybaseret tjeneste i modellen Klassisk installation, Azure-platformen opretter en ressource gruppenavn, der indeholder mønstret `cloud-service-name>-migrated`.

>[AZURE.NOTE] Virtuelle maskiner, der ikke er i et klassisk virtuelt netværk er stoppet deallocated i denne fase af overflytningen.

### <a name="check-manual-or-scripted"></a>Kontrollere (manuelt eller script)

Du kan eventuelt bruge konfigurationen, du har hentet tidligere til at validere, at overførslen ser rigtige trinnet afkrydsningsfelt. Alternativt kan kan du logge på portalen og kontrol egenskaber og ressourcer til at validere, metadata overførsel ser rigtigt ud.

Hvis du overfører et virtuelt netværk, er de fleste konfiguration af virtuelle maskiner ikke genstartet. For programmer på disse FOS, kan du kontrollere, at programmet er stadig op at køre.

Du kan teste din overvågning/automatisering og drift scripts til at se, om FOS fungerer som forventet, og hvis dine opdaterede scripts fungerer korrekt. Kun få handlinger understøttes, når ressourcerne, der er i tilstanden forberedt.

Der er ingen sæt tidsramme, som du vil acceptere overførslen. Du kan bruge så lang tid, som du vil have i denne tilstand. Management plan er dog låst for disse ressourcer, indtil du afbrydelse eller bekræftelse.

Hvis du får vist problemer, kan du altid afbryde overførslen og gå tilbage til den klassiske implementeringsmodel. Når du går tilbage, åbnes den Azure platform management-plan handlinger på ressourcerne, så du kan genoptage normal handlinger på disse FOS i modellen Klassisk installation.

### <a name="abort"></a>Afbryde

Afbryd er et valgfrit trin, du kan bruge til at gendanne dine ændringer til modellen Klassisk installation og stoppe overførslen.

>[AZURE.NOTE] Denne handling kan ikke udføres, når du har udløste allokeringen.  

### <a name="commit"></a>Anvend

Når du er færdig validering, kan du bekræfte overførslen. Ressourcer vises ikke længere i klassisk og er kun tilgængelig i implementeringsmodel ressourcestyring. De overførte ressourcer kan administreres kun i den nye portal.

>[AZURE.NOTE] Dette er en idempotent handling. Hvis det ikke lykkes, kan det anbefales, at du prøve igen. Hvis det fortsat mislykkes, skal oprette en supportbilletter eller oprette et forumindlæg med mærket ClassicIaaSMigration på vores [VM forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

**Denne overførselsplan påvirker af min eksisterende tjenester eller anvendelser, der kører på Azure virtuelle computere?**

Nej. De FOS (klassisk) er fuldt understøttet tjenester i generelt tilgængelig. Du kan fortsætte med at bruge disse ressourcer til at udvide din miljøet på Microsoft Azure.

**Hvad sker der med mine FOS, hvis jeg ikke har planer om at overføre inden for kort tid?**

Vi ikke går eksisterende klassisk API'er og ressource model. Vi vil gøre overførsel nemt, skelne de avancerede funktioner, der er tilgængelige i implementeringsmodel ressourcestyring. Det anbefales, at du gennemgår [nogle af fremskridt](virtual-machines-windows-compare-deployment-models.md) , der er en del af IaaS under ressourcestyring.

**Hvad betyder denne overførselsplan for min eksisterende værktøj?**

Opdatere din værktøj til implementeringsmodel ressourceleder, der er en af de vigtigste ændringer, der skal tage højde for i din migreringsplaner.

**Hvor lang tid bliver management-plan nedetid?**

Det afhænger af antallet ressourcer, der overføres. Hele overførslen fører til mindre installationer (et par mange af FOS), mindre end en time. Overflytningen kan tage et par timer for store installationer (hundredvis af FOS).

**Kan jeg gå tilbage, når min overflytning af ressourcer er anvendt i ressourcestyring?**

Du kan afbryde din migrering, som ressourcerne, der er i tilstanden forberedt. Rollback understøttes ikke, når ressourcerne, der er blevet overført via allokeringen.

**Kan jeg annullere mit overførsel hvis allokeringen mislykkes?**

Du kan afbryde overførsel, hvis allokeringen mislykkes. Alle handlinger til overførsel, herunder allokeringen, er idempotent. Så vi anbefaler, at du prøve igen efter kort tid. Hvis du stadig står over for en fejl, kan du oprette en supportbilletter eller oprette et forumindlæg med mærket ClassicIaaSMigration på vores [VM forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**Skal jeg anvende for at købe en anden udtrykkelig distribuere kredsløb, hvis jeg skal bruge IaaS under ressourcestyring?**

Nej. Vi aktiveret for nylig [flytte ExpressRoute kredsløb fra klassiske til implementeringsmodel ressourcestyring](../expressroute/expressroute-move.md). Du behøver ikke at købe et nyt ExpressRoute kredsløb, hvis du allerede har en.

**Hvad nu, hvis jeg havde konfigureret rollebaseret adgangskontrol politikker til mine klassisk IaaS ressourcer?**

Under overførslen transformere ressourcerne fra klassisk til Ressourcestyring. Vi anbefaler derfor, at du regner RBAC politik-opdateringer, der skal ske efter overførslen.

**Hvad nu, hvis jeg bruger Azure gendannelse af websteder eller Azure sikkerhedskopi i dag?**

Jeg har sikkerhedskopieret min klassisk FOS i sikkerhedskopiering samling af legitimationsoplysninger for at overføre din virtuelle maskine, der er aktiveret for sikkerhedskopiering, se [. Nu vil jeg overføre mit FOS fra tilstanden Klassisk til Ressourcestyring tilstand. Hvordan kan jeg forsøge at bruge dem i gendannelse services samling?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**Kan jeg kontrollere mine abonnement eller ressourcer for at se, hvis de er i stand til at overførsel?**

Ja. I indstillingen platform understøttes migrering er det første trin i forbindelse med forberedelsen til overførsel til at kontrollere, at ressourcerne, der er stand til at overførsel. I tilfælde af Valider handlingen ikke lykkes, modtager du meddelelser på grund af alle overførslen ikke kan gennemføres.

**Hvad sker der, hvis jeg støder på fejlen kvote under forberedelse IaaS ressourcer til overførsel?**

Vi anbefaler, at du afbryde din migrering, og log derefter en supportanmodning at øge kvoter i det område, hvor du overfører FOS. Når kvote anmodningen er godkendt, kan du begynde at afholde overførsel trinnene igen.

**Hvordan rapporterer jeg et problem?**

Sende din problemer og spørgsmål om overførsel til vores [VM forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows), med nøgleordet ClassicIaaSMigration. Vi anbefaler, at sende alle dine spørgsmål på denne-forummet. Hvis du har en support-kontrakt, er du Velkommen til at logge af en supportbilletter.

**Hvad nu, hvis jeg ikke kan lide navnene på de ressourcer, der vælger platformen under overførsel?**

Alle de ressourcer, du angiver eksplicit navne til i den klassiske implementeringsmodel bevares under overflytningen. I nogle tilfælde oprettes nye ressourcer. For eksempel: en netværksgrænseflade der oprettes for hver VM. Vi understøtter i øjeblikket ikke muligt at styre navnene på disse nye ressourcer, der oprettes under overførsel. Log din stemmer for denne funktion på [Azure feedback-forummet](http://feedback.azure.com).

* *jeg fik meddelelsen *"VM rapporterer overordnede agent status ikke er klar. Det vil sige, hvor VM kan ikke overføres. Sørg for, at VM Agent er rapportering om overordnede agent status som klar"* eller *"VM indeholder lokalnummer, hvis Status ikke bliver rapporteret fra VM. Det vil sige, denne VM kan ikke overføres."***

Denne meddelelse er modtaget, når VM ikke er udgående forbindelse til internettet. VM agent bruger udgående forbindelse til at oprette forbindelse til kontoen Azure lagerplads for at opdatere statusmeddelelsen agent for hver fem minutter.


## <a name="next-steps"></a>Næste trin
Nu hvor du har forstået overførslen af klassisk IaaS ressourcer til Ressourcestyring, kan du starte overflytningen ressourcer.

- [Tekniske undersøgelse på platform understøttes overførsel fra klassisk til Azure ressourcestyring](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Bruge PowerShell til at overføre IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Brug CLI til at overføre IaaS ressourcer fra klassisk til Azure ressourcestyring](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Klone en klassisk virtuel maskine til Azure ressourcestyring ved hjælp af PowerShell-scripts, community](virtual-machines-windows-migration-scripts.md)
