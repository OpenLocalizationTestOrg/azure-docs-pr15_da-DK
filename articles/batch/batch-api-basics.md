<properties
    pageTitle="Azure batchen funktionsoversigt for udviklere | Microsoft Azure"
    description="Lær funktionerne i tjenesten batchen og dens API'er fra et udvikling synspunkt."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Batchen funktionsoversigt for udviklere

I denne oversigt over de grundlæggende komponenter i tjenesten Azure batchen, gennemgås de primære tjeneste funktioner og ressourcer, som batchen udviklere kan bruge til at oprette store parallelt beregne løsninger.

Om du udvikler et distribueret computational program eller en tjeneste, som problemer direkte [REST-API] [ batch_rest_api] opkald, eller du bruger en af [Batchen SDK'er](batch-technical-overview.md#batch-development-apis), du skal bruge mange af de ressourcer og funktioner, der er beskrevet i denne artikel.

> [AZURE.TIP] Se [Grundlæggende af Azure batchen](batch-technical-overview.md)til et højere niveau Introduktion til tjenesten batchen.

## <a name="batch-service-workflow"></a>Batchen tjenesten arbejdsproces

Følgende overordnet arbejdsprocessen er typisk næsten alle programmer og tjenester, der bruger tjenesten batchen til at behandle parallelle arbejdsbelastninger:

1. Overføre **datafiler** , der skal behandles til en [Azure-lager] [ azure_storage] konto. Batchen indeholder indbygget understøttelse af adgang til Azure Blob-lager, og dine opgaver kan hente filerne til at [beregne noder](#compute-node) Når opgaverne udføres.

2. Overfør **programfiler** , som kører opgaver. Disse filer kan være binære filer eller scripts og deres afhængigheder, og er udført af opgaverne i dine sager. Opgaver kan hente filerne fra din lagerplads konto, eller du kan bruge funktionen [programpakker](#application-packages) i batchen til programadministration og installation.

3. Oprette en [gruppe](#pool) af Beregn knuder. Når du opretter en gruppe, kan du angive antallet Beregn noder til puljen, deres størrelse og operativsystemet. Når de enkelte opgaver i dit arbejde kører, er det tildelt til at udføre på en af noderne i din gruppe.

4. Oprette et [job](#job). Et job håndterer en samling af opgaver. Du knytte hver sag til en bestemt samling, hvor den arbejdsopgaver skal køres.

5. Føje [opgaver](#task) til jobbet. Hver enkelt opgave kører programmet eller scriptet, du har overført for at behandle den henter fra kontoen lagerplads datafiler. Som hver enkelt opgave er fuldført, kan det overføre dens output til Azure-lager.

6. Overvåge job fremskridt og hente opgave output fra Azure-lager.

I følgende afsnit beskrives disse og andre ressourcer af Batch, der aktiverer scenariet fordelt computational.

> [AZURE.NOTE] Du har brug for en [batchen konto](batch-account-create-portal.md) for at bruge tjenesten batchen. Næsten alle løsninger du også bruge en [Azure-lager] [ azure_storage] højde for fillagring og overførsel af dokumenter. Batch aktuelt understøtter kun **generelle formål** lagerplads kontotypen, som det er beskrevet i trin 5, [Opret en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) i [om Azure lagerplads konti](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Batchen serviceressourcer

Nogle af de følgende ressourcer – konti, beregne noder, grupper, job og opgaver – kræves af alle løsninger, der bruger tjenesten batchen. Andre, er som jobbet tidsplaner og programpakker, nyttigt, men valgfrie funktioner.

- [Konto](#account)
- [Beregne node](#compute-node)
- [Puljen](#pool)
- [Job](#job)

  - [Jobbet tidsplaner](#scheduled-jobs)

- [Opgave](#task)

  - [Start opgave](#start-task)
  - [Manager jobopgave](#job-manager-task)
  - [Jobbet forberedelse og release opgaver](#job-preparation-and-release-tasks)
  - [Opgave med flere forekomster (MPI)](#multi-instance-tasks)
  - [Opgaveafhængigheder](#task-dependencies)

- [Programpakker](#application-packages)

## <a name="account"></a>Konto

En batchen konto er en separat identificerede enhed i tjenesten batchen. Alle behandling er knyttet til en batchen konto. Når du udfører handlinger med tjenesten batchen, skal du både på kontonavnet og én af dens konto taster. Du kan [oprette en Azure batchen konto ved hjælp af portalen Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Beregne node

En beregningsnode er en Azure virtuel maskine (VM), der er dedikeret til at behandle en del af dit program arbejdsbyrde. Størrelsen på en node bestemmer antallet af CPU kerner, hukommelseskapacitet og lokale system filstørrelse, der er allokeret til noden. Du kan oprette grupper af Windows eller Linux knuder ved hjælp af Azure Cloud Services eller virtuelle maskiner Marketplace billeder. Se afsnittet nedenfor [puljen](#pool) kan finde flere oplysninger om disse indstillinger.

Noder kan køre en hvilken som helst eksekverbar fil eller et script, der understøttes af operativsystemmiljøet af noden. Dette omfatter \*.exe, \*cmd., \*.bat og PowerShell-scripts til Windows- og binære filer, udformning og Python scripts til Linux.

Alle beregne noder i Batch også indeholde:

- En almindelig [mappestrukturen](#files-and-directories) og tilknyttede [miljøvariabler](#environment-settings-for-tasks) , der er tilgængelige til brug af opgaver.
- **Firewall** -indstillinger, der er konfigureret til at styre adgangen.
- [Fjernadgang](#connecting-to-compute-nodes) til både Windows (RDP Remote Desktop Protocol ()) og noder Linux (Secure Shell (SSH)).

## <a name="pool"></a>Puljen

En samling er en samling af knuder, dit program kører på. Puljen kan oprettes manuelt af dig eller automatisk af tjenesten batchen når du angiver arbejdet, der udføres. Du kan oprette og administrere en gruppe, der opfylder ressourcebehovet for dit program. En samling kan anvendes kun af kontoen batchen, hvor det blev oprettet. En batchen konto kan have mere end én puljen.

Azure batchen grupper opbygge oven på core Azure Beregn-platform. De giver store allokering, installation af programmer, datafordeling, sundhedsovervågning og fleksible justering af antallet af knuder Beregn inden for en gruppe ([Skalering](#scaling-compute-resources)).

Hver node, der er føjet til en gruppe er tildelt et entydigt navn og IP-adresse. Når en node er fjernet fra en gruppe, går tabt, eventuelle ændringer, der er foretaget i operativsystemet eller filer, og dets navn og IP-adresse er udgivet til senere brug. Når en node forlader en samling, er dets levetid over.

Når du opretter en gruppe, kan du angive følgende egenskaber:

- Beregne node **operativsystem** og **version**

    Har du to muligheder, når du vælger et operativsystem for knuderne i din gruppe: **Virtuelt konfiguration** og **Konfiguration af Cloud Services**.

    **Virtuelt konfiguration** indeholder både Linux og Windows billeder til beregne noder fra [Azure virtuelle maskiner Marketplace][vm_marketplace].
    Når du opretter en gruppe, som indeholder virtuelt konfiguration noder, skal du angive ikke kun størrelsen på noderne, men også **virtuelt billede reference** og batchen **node agent SKU** skal være installeret på noderne. Du kan finde flere oplysninger om angivelse af disse puljen egenskaber, se [klargøring Linux beregne knuder på Azure batchen grupper](batch-linux-nodes.md).

    **Konfiguration af Cloud Services** indeholder Windows beregne noder *kun*. Tilgængelige operativsystemer til konfiguration af Cloud Services grupper vises [Azure gæst OS versioner og SDK kompatibilitet matrix](../cloud-services/cloud-services-guestos-update-matrix.md). Når du opretter en gruppe, som indeholder Cloud Services noder, skal du angive nodestørrelsen og dens *OS familie*. Når du opretter grupper af Windows beregne noder, du bruger oftest Cloud Services.

    - *OS familie* også bestemmer, hvilke versioner af .NET der installeres med Operativsystemet.
    - Som med arbejder roller i Cloud Services, kan du angive en *Version af Operativsystemet* (flere oplysninger om arbejder roller, i afsnittet [Fortæl mig om skytjenester](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) i [Oversigt over Cloud Services](../cloud-services/cloud-services-choose-me.md)).
    - Når med arbejder roller, anbefaler vi, at du angiver `*` for den *Version af Operativsystemet* , så noderne opgraderes automatisk, og der ikke er nogen arbejde, der kræves for at imødekomme til nyligt udgivet versioner. Den primære use case for at vælge en bestemt OS-version er at sikre kompatibilitet programmer, som giver mulighed for bagudkompatibilitet test skal udføres, før du tillader version der skal opdateres. Efter validering, *OS-Version* til puljen kan blive opdateret, og det nye OS billede kan være installeret – en igangværende opgaver er afbrudt og sat i kø igen.

- **Størrelse for knuderne**

    **Konfiguration af Cloud Services** Beregn node størrelser, findes i [størrelser til Skytjenester](../cloud-services/cloud-services-sizes-specs.md). Batchen understøtter alle Cloud Services størrelser undtagen `ExtraSmall`.

    **Virtuelt konfiguration** beregne node størrelser, findes i [størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) og [størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Batchen understøtter alle Azure VM størrelser undtagen `STANDARD_A0` og dem med premium lagerplads (`STANDARD_GS`, `STANDARD_DS`, og `STANDARD_DSV2` serie).

    Når du vælger en Beregn node størrelse, skal du overveje egenskaber og de programmer, du kører på noderne krav. Aspekter som om programmet er flertrådet, og hvor meget forbruger hukommelse kan hjælpe bestemme mest passende og økonomisk node størrelse. Det er typisk til at vælge en node størrelse Hvis en opgave kan køre på en node ad gangen. Men det er muligt for at have flere opgaver (og dermed flere forekomster af tjenesteprogrammer) [køre parallelt](batch-parallel-node-tasks.md) på beregne noder under kørsel af jobbet. I dette tilfælde er det almindelige at vælge en større knude til den stigende efterspørgsel af udførelse af parallel opgave. Yderligere oplysninger finder du i [planlægningen for opgaven](#task-scheduling-policy) .

    Alle knuderne i en gruppe har samme størrelse. Hvis har til hensigt at køre programmer med forskellige systemkrav og/eller indlæse niveauer, anbefales det, at du bruger separate grupper.

- **Target antallet af knuder**

    Dette er antallet af Beregn knuder, du vil installere i puljen. Dette kaldes en *destination* fordi i nogle situationer ikke kan din puljen når det ønskede antal noder. En samling muligvis ikke frem til det ønskede antal noder, hvis det når [core kvote](batch-quota-limit.md#batch-account-quotas) for kontoen batchen – eller hvis der er en automatisk skalering formel, som du har anvendt til puljen, der begrænser det maksimale antal noder (se afsnittet "Skalering politik" nedenfor).

- **Skalering politik**

    Ud over at angive en statisk antallet af knuder, kan du i stedet skrive og anvende en [automatisk skalering formel](#scaling-compute-resources) til en gruppe. Tjenesten batchen evaluerer formlen og justerer antallet af knuder i gruppen baseret på forskellige puljen, job og opgave parametre, som du kan angive med jævne mellemrum.

- **Politik om Opgaveplanlægning**

    Indstillingen [Maks opgaver per node](batch-parallel-node-tasks.md) konfiguration bestemmer det maksimale antal opgaver, der kan køres parallelt på hver beregningsnode i gruppen.

    Standardkonfigurationen er, at en opgave ad gangen, der kører på en node, men der er scenarier, hvor det er en fordel at have mere end én opgave, der udføres på en node samtidigt. Se [eksempel scenario](batch-parallel-node-tasks.md#example-scenario) i [samtidige node opgaver](batch-parallel-node-tasks.md) artiklen for at se, hvordan du kan få glæde af flere opgaver per node.

    Du kan også angive en *Fyld-type* , som bestemmer, om batchen opslag opgaverne jævnt på tværs af alle noder i en gruppe, eller pakker hver node med det maksimale antal opgaver før tildele opgaver til en anden node.

- **Kommunikation status** for Beregn noder

    I de fleste scenarier opgaver betjene uafhængigt af hinanden og er ikke nødvendigt at kommunikere med hinanden. Der er dog nogle programmer, hvor opgaver skal kommunikere, som [MPI scenarier](batch-mpi.md).

    Du kan konfigurere en gruppe for at tillade kommunikation mellem noder inden for it -**internode kommunikation**. Når internode kommunikation er aktiveret, knuder på konfiguration af Cloud Services grupper kan kommunikere med hinanden på porte, der er større end 1100 og virtuelt konfiguration grupper Begræns ikke trafik på en hvilken som helst port.

    Bemærk, at aktivere internode kommunikation også har indflydelse på placeringen af noderne i klynger og kan begrænse det maksimale antal noder i en gruppe på grund af installation begrænsninger. Hvis dit program ikke kræver kommunikation mellem noder, tjenesten batchen kan tildele et potentielt stort antal noder til puljen fra mange forskellige klynger og datacentre til at aktivere øget parallel behandling power.

- **Starte opgave** for Beregn noder

    Valgfri *starter opgave, der* udføres på hver node, som noden deltagere puljen, hver gang en node genstartes eller reimaged. Start opgaven er især nyttig til forberedelse af Beregn noder til udførelse af opgaver som at installere de programmer, som dine opgaver, der kører på noderne Beregn.

- **Programpakker**

    Du kan angive [programpakker](#application-packages) skal installeres til Beregn knuderne i puljen. Programpakker giver forenklet implementering og versionsstyring de programmer, der køres opgaverne. Programpakker, du angiver for en gruppe er installeret på hver knude, der forbinder denne gruppe, og hver gang en node genstartet eller reimaged. Programpakker er i øjeblikket ikke understøttes på Linux Beregn noder.

- **Netværkskonfiguration**

    Du kan angive et Azure [virtuelt netværk (VNet)](../virtual-network/virtual-networks-overview.md) , hvor puljen beregne noder ID skal oprettes. Krav til at angive en VNet for din gruppe kan findes i [Tilføj en gruppe til en konto] [ vnet] i batchen REST-API reference.

> [AZURE.IMPORTANT] Alle batchen konti har en standard- **kvote** , som begrænser antallet af **kerner** (og dermed, Beregn noder) på en batchen konto. Du kan finde standardkvotaer og oplysninger om, hvordan at [øge en kvote](batch-quota-limit.md#increase-a-quota) (som det maksimale antal kerner i kontoen batchen) i [kvotaer og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md). Hvis du kan finde dig selv bede "Hvorfor ikke min puljen når mere end X noder?" Denne core kvote kan være årsagen.

## <a name="job"></a>Job

Et job er en samling af opgaver. Den håndterer hvordan beregning udføres af dens opgaver på Beregn knuderne i en gruppe.

- Jobbet angiver **puljen** , er arbejdet, der skal køres. Du kan oprette en ny gruppe for hvert job, eller brug en puljen for mange job. Du kan oprette en gruppe for hver sag, som er knyttet til en tidsplan for jobbet eller for alle job, der er knyttet til en tidsplan for jobbet.

- Du kan angive en valgfri **jobprioritet**. Når et job sendes med en højere prioritet end job der i øjeblikket er i gang, indsættes opgaverne for højere prioritet jobbet i køen forud for opgaver for de lavere priority job. Opgaver i lavere prioritet job, der kører allerede er ikke tilsidesættes.

- Du kan bruge jobbet **begrænsninger** til at angive visse grænser for dine sager:

    Du kan angive et **maksimalt wallclock tid**, så hvis en sag kører i længere tid end den maksimale wallclock tid, der er angivet, jobbet og alle dens opgaver er afsluttet.

    Batchen kan registrere og derefter prøve igen mislykkede opgaver. Du kan angive det **maksimale antal opgave forsøg** som en betingelse, herunder om en opgave er *altid* eller *aldrig* forsøgt igen. Gentage en opgave betyder, at opgaven er sat i kø igen skal køres igen.

- Klientprogrammet kan føje opgaver til et job, eller du kan angive en [jobopgave manager](#job-manager-task). En jobopgave manager indeholder de oplysninger, der er nødvendige for at oprette de påkrævede opgaver for en sag med jobopgave manager der kører på en af Beregn knuderne i puljen. Manager jobopgave håndteres specifikt i Batch – det er i kø snart jobbet oprettes, og genstartes, hvis det ikke lykkes. En jobopgave manager er *påkrævet* for stillinger, der er oprettet af en [sag tidsplan](#scheduled-jobs) , fordi det er den eneste måde at definere opgaverne, før jobbet er en forekomst.

- Som standard forbliver job i den aktive tilstand, når alle opgaver i jobbet er fuldført. Du kan ændre denne funktionsmåde, så jobbet afbrydes automatisk, når alle opgaverne i jobbet er fuldført. Angive det job **onAllTasksComplete** egenskab ([OnAllTasksComplete] [ net_onalltaskscomplete] i batchen .NET) til *terminatejob* til automatisk at afbryde jobbet, når alle dens opgaver er i tilstanden færdige.

    Bemærk, at tjenesten batchen finder et job med *nogen* opgaver, har alle dens fuldførte opgaver. Denne indstilling bruges derfor oftest med en [jobopgave manager](#job-manager-task). Hvis du vil bruge automatisk jobbet afslutning uden en sag manager, skal du først angive et nyt job **onAllTasksComplete** egenskab til *noaction*derefter angive den til *terminatejob* , når du er færdig med at føje opgaver til jobbet.

### <a name="job-priority"></a>Jobprioritet

Du kan tildele en prioritet til sager, som du opretter i Batch. Tjenesten batchen bruger værdien fra priority på sag til at bestemme rækkefølgen af jobplanlægning i en konto, (dette er ikke at forveksles med et [planlagte jobbet](#scheduled-jobs)). Prioriteten værdier er mellem-1000 til 1000 med-1000 er den laveste prioritet og 1000 den højeste. Du kan opdatere prioriteten af en sag ved hjælp af den [Opdater egenskaberne for et job] [ rest_update_job] handling (batchen RESTEN) eller ved at ændre [CloudJob.Priority] [ net_cloudjob_priority] egenskaben (batchen .NET).

I den samme konto har højere prioritet job planlægning rangorden for over lavere prioritet job. En sag med en højere prioritet værdi i én konto har ikke planlægning rangorden for over et andet job med en lavere prioritet værdi i en anden konto.

Jobbet planlægning på tværs af grupper er uafhængig. Mellem forskellige grupper, er det ikke sikkert, at et job højere prioritet planlægges først, hvis dens tilknyttede puljen er under inaktiv noder. Job med det samme prioritetsniveau har en lig risikoen for, at der planlægges i den samme gruppe.

### <a name="scheduled-jobs"></a>Planlagte opgaver

[Job tidsplaner] [ rest_job_schedules] gør det muligt at oprette tilbagevendende job i tjenesten batchen. En tidsplan for jobbet Angiver hvornår du skal køre job og omfatter specifikationer for opgaverne skal udføres. Du kan angive varigheden af tidsplanen – hvor lang tid og når planen er aktiv – og hvor ofte i denne periode, der skal oprettes job.

## <a name="task"></a>Opgave

En opgave er en måleenhed beregning, der er knyttet til en sag. Den kører på en node. Opgaver er tildelt til en node til udførelse af eller er i kø, indtil en node bliver gratis. En opgave, der placerer blot, kører en eller flere programmer eller scripts på en beregningsnode at udføre arbejdet, du har brug for færdig.

Når du opretter en opgave, kan du angive:

- **Kommandolinjen** af opgaven. Dette er den kommando, der kører programmet eller script på noden Beregn.

    Det er vigtigt at være opmærksom på, at ikke faktisk kører kommandolinjen under en shell. Det kan derfor oprindeligt drage fordel af shellfunktioner som [miljøvariablen](#environment-settings-for-tasks) udvidelse (Dette omfatter de `PATH`). For at kunne udnytte disse funktioner, du skal aktivere shell i kommandolinjen – for eksempel ved at starte `cmd.exe` på Windows noder eller `/bin/sh` på Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Hvis dine opgaver skal køre et program eller script, som ikke er i noden `PATH` eller referencetype miljøvariabler, skal du aktivere shell eksplicit i kommandolinjen opgave.

- **Ressourcefiler** , der indeholder dataene, der skal behandles. Disse filer kopieres automatisk til noden fra Blob-lager på en konto til **generelle formål** Azure-lager, før der udføres opgavens kommandolinjen. Du kan finde flere oplysninger i afsnittene [Start opgave](#start-task) og [filer og mapper](#files-and-directories).

- De **miljøvariabler** , der kræves af dit program. I afsnittet [miljøindstillinger for opgaver](#environment-settings-for-tasks) kan finde flere oplysninger.

- De **begrænsninger** , som opgaven skal udføres. For eksempel, den maksimale tid, som opgaven er tilladt at køre den maksimalt tilladte antal gange, der skal forsøges igen en mislykkedes opgave, og maksimalt tid, der filer i opgavens arbejdsmappe bevares.

- **Programpakker** skal installeres til noden Beregn, som opgaven er planlagt til at køre. [Programpakker](#application-packages) giver forenklet implementering og versionsstyring de programmer, der køres opgaverne. Opgaveniveau programpakker er især nyttig i delt puljen miljøer, hvor forskellige job køres på en gruppe og puljen slettes ikke, når en sag er afsluttet. Hvis din sag har opgaver, der er færre end noder i puljen, kan opgave programpakker minimere dataoverførsel da din programmet installeres kun for knuderne, der kører opgaver.

Ud over de opgaver, du definerer for at udføre beregning på en node findes følgende særlige opgaver også af tjenesten batchen:

- [Start opgave](#start-task)
- [Manager jobopgave](#job-manager-task)
- [Jobbet forberedelse og release opgaver](#job-preparation-and-release-tasks)
- [Flere forekomster opgaver (MPI)](#multi-instance-tasks)
- [Opgaveafhængigheder](#task-dependencies)

### <a name="start-task"></a>Start opgave

Ved at knytte en **start opgave** til en gruppe, kan du forberede operativmiljø af dens knuder. Du kan for eksempel udføre handlinger som installerer de programmer, der køres opgaverne, og starter baggrundsprocesser. Start opgaven kører, hver gang en node starter, for så længe den forbliver i puljen – herunder, når du starter noden føjes til puljen, og når det er genstartet eller reimaged.

En primær fordelen ved start opgaven er, at det kan indeholde alle de oplysninger, der er nødvendige for at konfigurere en beregningsnode og installere de programmer, der kræves til udførelse af opgave. Derfor øge antallet af knuder på en gruppe er så enkelt som angive den nye destination node optælling – batchen allerede har de oplysninger, der skal bruges til at konfigurere nye knuderne og gøre dem klar til at acceptere opgaver.

Som med Azure batchen opgaver, kan du angive en liste over **ressourcefiler** i [Azure-lager][azure_storage], ud over en **kommandolinjen** skal udføres. Batchen først kopieres Ressourcefilerne til noden fra Azure-lager og derefter køres kommandolinjen. For en puljen start opgave indeholder fillisten typisk programmet opgave og dets afhængigheder.

Det kan også indeholde referencedata der skal bruges i alle opgaver, der kører på beregningsnode. For eksempel en start opgave kommandolinjen kan udføre en `robocopy` handling til at kopiere programfiler (som er angivet som ressourcefiler og hentet til noden) fra start opgavens [arbejdsmappe](#files-and-directories) til den [delte mappe](#files-and-directories)og derefter køre en MSI eller `setup.exe`.

> [AZURE.IMPORTANT] Batch aktuelt understøtter *kun* kontotypen **generelle formål** lagerplads som beskrevet i trin 5, [Opret en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) i [om Azure lagerplads konti](../storage/storage-create-storage-account.md). Batchopgaver (herunder almindelige opgaver, start opgaver, arbejdsopgaver forberedelse og release arbejdsopgaver) skal angive ressourcefiler, der findes *kun* i **generelle formål** lagerplads konti.

Typisk bør for tjenesten batchen skal vente på start opgaven er afsluttet før hensyn noden klar til at være tildelt opgaver, men du kan konfigurere dette.

Hvis der er en mislykkes på en beregningsnode start, derefter tilstanden for noden opdateres afspejler fejlen, og noden er tildelt ikke opgaver. En start-opgave kan mislykkes, hvis der er et problem, kopiere ressourcefilerne fra lagerplads, eller hvis den proces, der er udført af dens kommandolinjen returnerer en undtagen nul afslutningskode.

Hvis du tilføjer eller opdaterer start opgaven til en *eksisterende* gruppe, skal du genstarte dens Beregn noder for start opgaven skal anvendes på noderne.

### <a name="job-manager-task"></a>Manager jobopgave

Du typisk brug en **jobopgave manager** til at styre og/eller overvåge job udførelse af – for eksempel til at oprette og sende opgaver for en sag, skal du bestemme flere opgaver til at køre, og bestemme, hvornår arbejde er fuldført. En jobopgave manager er dog ikke begrænset til disse aktiviteter. Det er en fuldt fledged opgave, der kan udføre handlinger, der kræves til jobbet. En jobopgave manager kan for eksempel hente en fil, der er angivet som en parameter, analysere indholdet af den pågældende fil og sende flere opgaver på baggrund af disse indholdet.

En jobopgave manager er i gang før alle andre opgaver. Den indeholder følgende funktioner:

- Den sendes automatisk som en opgave af tjenesten batchen når jobbet oprettes.

- Det er planlagt til at udføre før andre opgaver i en sag.

- Dens tilknyttede node er sidst at blive fjernet fra en gruppe, når puljen er blive reduceret.

- Dens opsigelse før kan knyttes til afslutningen af alle opgaverne i jobbet.

- En jobopgave manager gives den højeste prioritet, når det skal genstartes. Hvis en inaktiv node ikke er tilgængelig, kan tjenesten batchen afslutte en af de andre igangværende opgaver i gruppen til at skabe plads til manager jobopgave til at køre.

- En jobopgave manager i ét job har ikke prioritet over opgaver af andre job. På tværs af sager, kun job niveau prioriteter skal være opfyldt.

### <a name="job-preparation-and-release-tasks"></a>Jobbet forberedelse og release opgaver

Batchen indeholder forberedelse arbejdsopgaver til udførelse af foreløbig job konfiguration. Jobbet release opgaver er beregnet til efter job vedligeholdelse eller oprydning.

- **Forberedelse af jobopgave**: en jobopgave forberedelse kører på alle Beregn noder, der er planlagt til at køre opgaver, før andre job opgaver skal udføres. Du kan bruge en jobopgave forberedelse til at kopiere data, der er fælles for alle opgaver, men er entydige for jobbet, f.eks.
- **Jobbet Frigiv opgave**: Når en sag er afsluttet, et job Frigiv opgave kører på hver node i puljen, udføres mindst én opgave. Du kan bruge et job Frigiv opgave til at slette data, der kopieres ved forberedelse jobopgave, eller for at komprimere og overføre diagnosticering logdata, f.eks.

Begge job forberedelse og release opgaver gør det muligt at angive en kommandolinje skal køres, når opgaven startes. De indeholder funktioner som filoverførsel, øgede udførelse af, brugerdefineret miljøvariabler, maksimale udførelse af varighed, antal forsøg og klokkeslæt i filen opbevaring.

Se [Kør jobbet forberedelse og fuldførelse opgaver på Azure batchen beregne noder](batch-job-prep-release.md)kan finde flere oplysninger om jobbet forberedelse og release opgaver.

### <a name="multi-instance-task"></a>Opgave med flere forekomster

En [med flere forekomster opgave](batch-mpi.md) er en opgave, der er konfigureret til at køre på mere end én beregningsnode samtidigt. Med flere forekomster opgaver, kan du aktivere high-performance computing scenarier, der kræver en gruppe af Beregn noder, der er allokeret sammen for at behandle en enkelt arbejdsbyrde (som der passerer MPI (Message Interface)).

Se [bruge flere forekomster opgaver til at køre der passerer MPI (Message Interface) programmer i Azure Batch](batch-mpi.md)en detaljeret beskrivelse på køre MPI job i Batch ved hjælp af biblioteket batchen .NET.

### <a name="task-dependencies"></a>Opgaveafhængigheder

[Opgaveafhængigheder](batch-task-dependencies.md)som navnet antyder, kan du angive, at en opgave afhænger af fuldførelse af andre opgaver før kørslen. Denne funktion giver understøttelse af situationer, hvor en "efterfølgende" opgave forbruger outputtet af en "før" opgave-, eller når en foregående opgave udfører nogle initialisering, der kræves af en efterfølgende opgave. Hvis du vil bruge denne funktion, skal du først aktivere opgaveafhængigheder på kørslen. Derefter skal du for hver opgave, som afhænger af en anden (eller mange andre), skal du angive de opgaver, som afhænger af denne opgave.

Med afhængigheder af opgaver, kan du konfigurere scenarier ud som følger:

* *taskB* afhænger af *taskA* (*taskB* ikke begynder udførelse af indtil *taskA* er afsluttet).
* *taskC* afhænger af både *taskA* og *taskB*.
* *taskD* afhænger af en række opgaver såsom at opgaver *1* - *10*, før den udfører.

Se [opgaveafhængigheder i Azure Batch](batch-task-dependencies.md) og [TaskDependencies] [ github_sample_taskdeps] kodeeksempel i [azure-batchen-eksempler] [ github_samples] GitHub lager yderligere detaljerede oplysninger om denne funktion.

## <a name="environment-settings-for-tasks"></a>Miljøindstillinger for opgaver

De enkelte opgaver, der er udført af tjenesten batchen har adgang til miljøvariabler, der angives på Beregn noder. Dette omfatter miljøvariabler, der er defineret af tjenesten Batch ([service-defineret][msdn_env_vars]) og brugerdefinerede miljøvariabler, som du kan definere for dine opgaver. Programmer og scripts udføre opgaver du har adgang til disse miljøvariabler under kørsel af.

Du kan angive brugerdefinerede miljøvariabler på niveauet for opgave eller et job ved at udfylde egenskaben *miljøindstillinger* for disse enheder. For eksempel se [tilføje en opgave til en sag] [ rest_add_task] handling (batchen REST-API) eller [CloudTask.EnvironmentSettings] [ net_cloudtask_env] og [CloudJob.CommonEnvironmentSettings] [ net_job_env] egenskaber i batchen .NET.

Din klientprogrammet eller tjenesten kan få en opgaves miljøvariabler, både service-defineret og brugerdefinerede, ved hjælp af den [få oplysninger om en opgave] [ rest_get_task_info] handling (batchen RESTEN) eller ved at åbne [CloudTask.EnvironmentSettings] [ net_cloudtask_env] egenskaben (batchen .NET). Processer, der kører på en beregningsnode kan få adgang til disse og andre miljøvariabler på noden, for eksempel ved hjælp af velkendte `%VARIABLE_NAME%` (Windows) eller `$VARIABLE_NAME` (Linux) syntaks.

Du kan finde en komplet liste over alle service brugerdefineret miljøvariabler i [beregne node miljøvariabler][msdn_env_vars].

## <a name="files-and-directories"></a>Filer og mapper

Hver enkelt opgave har en *arbejdsmappe* under, programmet opretter nul eller flere filer og mapper. Denne arbejdsmappe kan bruges til lagring af det program, der køres i opgaven, de data, det behandler og output fra behandling udfører den. Alle filer og mapper for en opgave ejes af brugeren opgave.

Tjenesten batchen Fremviser en del af filsystemet på en node som *rodmappen*. Opgaver kan få adgang til rodmappen ved at referere til den `AZ_BATCH_NODE_ROOT_DIR` miljøvariablen. Du kan finde flere oplysninger om brug af miljøvariabler [miljøindstillinger for opgaver](#environment-settings-for-tasks).

Rodmappen indeholder følgende mappestrukturen:

![Beregne node mappestrukturen][1]

- **delte**: denne mappe giver læse-/ skriveadgang til *alle* opgaver, der kører på en node. Alle opgaver, der kører på noden kan oprette, læse, opdatere og slette filer i denne mappe. Opgaver kan få adgang til denne mappe ved at referere til den `AZ_BATCH_NODE_SHARED_DIR` miljøvariablen.

- **Start**: denne mappe bruges af en start-opgave som dens arbejdsmappe. Alle de filer, der hentes til noden ved start opgaven er gemt her. Start opgaven kan oprette, læse, opdatere og slette filer under denne mappe. Opgaver kan få adgang til denne mappe ved at referere til den `AZ_BATCH_NODE_STARTUP_DIR` miljøvariablen.

- **Opgaver**: en mappe oprettes for hver opgave, der kører på noden. Der er adgang til ved at referere til den `AZ_BATCH_TASK_DIR` miljøvariablen.

    I hver enkelt opgave systembiblioteket tjenesten batchen opretter en arbejdsmappe (`wd`) Hvis entydige sti er angivet af den `AZ_BATCH_TASK_WORKING_DIR` miljøvariablen. Denne mappe indeholder læse-/ skriveadgang til opgaven. Opgaven kan oprette, læse, opdatere og slette filer under denne mappe. Denne mappe bevares baseret på den *RetentionTime* begrænsning, der er angivet for opgaven.

    `stdout.txt`og `stderr.txt`: filerne skrives til mappen med opgaver under udførelse af opgaven.

>[AZURE.IMPORTANT] Når en node er fjernet fra puljen, fjernes *alle* de filer, der er gemt på noden.

## <a name="application-packages"></a>Programpakker

Funktionen [programpakker](batch-application-packages.md) giver nem administration og installation af programmer til Beregn knuderne i dine grupper. Du kan overføre og administrere flere versioner af de programmer, som køres af dine opgaver, herunder deres binære filer og support-filer. Derefter kan du automatisk installere en eller flere af disse programmer til noderne Beregn i din gruppe.

Du kan angive programpakker på niveauet for puljen og opgave. Når du angiver programpakker til puljen, programmet er installeret på hver node i puljen. Når du angiver opgave-programpakker, at programmet installeres kun for noder, der er planlagt til at køre mindst én af de job opgaver, lige før opgavens kommandolinjen kører.

Batchen håndterer detaljerne for at arbejde med Azure-lager til at gemme dine programpakker og installere dem for at beregne noder, så både kode og administration af omkostninger bliver nemmere.

Hvis du vil have mere at vide om funktionen programmet pakke, se [program-installation med Azure batchen programpakker](batch-application-packages.md).

>[AZURE.NOTE] Hvis du føjer puljen programpakker til en *eksisterende* gruppe, skal du genstarte dens Beregn noder til de programpakker skal installeres til noderne.

## <a name="pool-and-compute-node-lifetime"></a>Puljen og Beregn node levetid

Når du designer din Azure batchen løsning, er det nødvendigt at foretage et design beslutning om, hvordan og, når grupper er oprettet, og hvor lang tid beregne noder i disse grupper holdes tilgængelige.

I den ene ende af spektret, kan du oprette en gruppe for hvert job, du sender og Slet puljen, så snart udførelse af er færdig med sine opgaver. Dette maksimerer anvendelsen da noderne allokeres kun, når det er nødvendigt, og luk så snart de er inaktiv. Det er vigtigt at være opmærksom på, at opgaver planlægges til udførelse af, så snart noder fås enkeltvis, allokeres, og start opgaven er afsluttet, mens det betyder, at jobbet skal vente på noderne, der skal tildeles. Batchen betyder *ikke* Vent, indtil alle noder i en gruppe er tilgængelige, før du tildeler opgaver til noderne. Dette sikrer maksimale anvendelsen af alle tilgængelige noder.

I anden ende af spektret, hvis har job starte umiddelbart er den højeste prioritet, kan du oprette en samling forvejen og gøre dens noder tilgængelig, før der sendes udskriftsjob. I dette scenarie skal opgaver kan starte med det samme, men noder kan være inaktive, mens du venter på at de skal tildeles.

En kombineret metode bruges typisk til håndtering af en variabel, men igangværende, belastning. Du kan have en samling, flere job sendes til, men kan skalere antallet af knuder op eller ned i henhold til job indlæse (se [Skalering beregne ressourcer](#scaling-compute-resources) i afsnittet nedenfor). Du kan gøre dette overdimensionere netværket, baseret på aktuelle belastning eller proaktiv, hvis belastning kan forudsiges.

## <a name="scaling-compute-resources"></a>Skalering Beregn ressourcer

Med [automatisk skalering](batch-automatic-scaling.md), kan du få tjenesten batchen dynamisk justere antallet af knuder Beregn i en gruppe i henhold til den aktuelle arbejdsbelastningen og Ressourceforbrug af scenariet Beregn. Dette giver dig mulighed at reducere de samlede omkostninger ved at køre dit program ved hjælp af kun de ressourcer, du har brug for, og slippe dem, du ikke har brug for.

Du aktiverer automatisk skalering ved at skrive en [automatisk skalering formel](batch-automatic-scaling.md#automatic-scaling-formulas) og knytte denne formel til en gruppe. Tjenesten batchen bruger formlen til at bestemme målliste antallet af knuder i puljen for det næste skalering interval (et interval, som du kan konfigurere). Du kan angive indstillingerne for automatisk skalering for en gruppe, når du opretter den, eller aktiverer skalering på en samling senere. Du kan også opdatere skalering indstillingerne på en skalering aktiverede puljen.

Som eksempel måske et job kræver, at du sender en meget store antal opgaver skal udføres. Du kan tildele en skalering formel til puljen, justerer antallet af knuder på den gruppe, der er baseret på det aktuelle antal i kø opgaver og den fuldførelse rente opgaverne i jobbet. Tjenesten batchen med jævne mellemrum evalueres formlen og ændrer størrelsen puljen, baseret på arbejdsbelastningen (tilføje noder til mange i kø opgaver og fjerne noder til ingen i kø eller igangværende opgaver) og din formel andre indstillinger.

En skalering formel kan være baseret på de følgende målepunkter:

- **Tid målepunkter** er baseret på Statistik indsamlet alle fem minutter i det angivne antal timer.

- **Ressource målepunkter** er baseret på CPU-forbrug, brugen af båndbredde, hukommelsesforbrug og antallet af knuder.

- **Opgave målepunkter** er baseret på opgavestatus, såsom *aktive* (i kø), *kører*eller *afsluttet*.

Når automatisk skalering reducerer antallet af Beregn knuder i en gruppe, skal du overveje Sådan håndteres opgaver, der kører på tidspunktet for handlingen Formindsk. For at imødekomme, indeholder batchen *node Deallokeringspolitik indstilling* , du kan medtage i dine formler. For eksempel kan du angive, at igangværende opgaver er ikke længere med det samme, ikke længere med det samme og derefter sat i kø igen til udførelse af på en anden node eller tilladelse til at afsluttes, før noden fjernes fra puljen.

Finde flere oplysninger om automatisk skalering et program, [Automatisk skala beregne knuder på en Azure batchen puljen](batch-automatic-scaling.md).

> [AZURE.TIP] Angive mål antallet af knuder nul i slutningen af en sag, men Tillad kører opgaver for at afslutte for at maksimere Beregn ressourceforbrug mere effektivt.

## <a name="security-with-certificates"></a>Sikkerhed med certifikater

Du typisk brug for at bruge certifikater, når du kryptere eller dekryptere følsomme oplysninger til opgaver, som nøglen for en [Azure-lager konto][azure_storage]. For at understøtte denne, kan du installere certifikater på noder. Krypteret hemmeligheder overføres til opgaver via kommandolinjeparametre eller integreret i en af ressourcerne, der opgave, og de installerede certifikater kan bruges til at dekryptere dem.

Du bruger [Tilføj certifikat] [ rest_add_cert] handling (batchen RESTEN) eller [CertificateOperations.CreateCertificate] [ net_create_cert] metode (batchen .NET) til at føje et certifikat til en batchen konto. Du kan derefter knytte certifikatet til en ny eller eksisterende gruppe. Når et certifikat er knyttet til en gruppe, installerer tjenesten batchen certifikatet på hver node i puljen. Tjenesten batchen installerer de relevante certifikater, når noden startes, før du starter en hvilken som helst opgaver (herunder på start og job manager opgave).

Hvis du tilføjer certifikater til en *eksisterende* gruppe, skal du genstarte dens Beregn noder til certifikaterne, der anvendes på noderne.

## <a name="error-handling"></a>Håndtering af fejl

Det kan være nødvendigt at håndtere både opgave og programmet fejl i din batchen løsning.

### <a name="task-failure-handling"></a>Håndtering af fejl ved opgave
Opgave mislykkede forsøg falder inden for disse kategorier:

- **Planlægning af fejl**

    Hvis overførslen af filer, der er angivet for en opgave af en eller anden grund ikke lykkes, angives "Planlægning fejl" for opgaven.

    Planlægge fejl kan opstå, hvis opgavens ressourcefiler, er flyttet, kontoen lagerplads er ikke længere tilgængelig, eller et andet problem blev fundet, forhindret vellykket kopiering af filer til noden.

- **Programfejl**

    Den proces, der er angivet af opgavens kommandolinjen kan også mislykkes. Processen anses for at have mislykkedes, når en undtagen nul afslutningskode, der returneres af den proces, der skal udføres ved opgaven (se *opgave afslutte koder* i næste afsnit).

    For programmet fejl, kan du konfigurere batchen for at gentage automatisk en opgave frem til et angivet antal gange.

- **Begrænsning fejl**

    Du kan angive en begrænsning, der angiver den maksimale udførelse af varighed for et job eller en opgave, *maxWallClockTime*. Det kan være nyttige til afslutning "hænger" opgaver.

    Når du har overskredet det maksimale antal timer, opgaven er markeret som *fuldført*, men afslutningskoden er indstillet til `0xC000013A` og feltet *schedulingError* er markeret som `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Fejlfinding programfejl

- `stderr`og`stdout`

    Et program kan give diagnosticering output, som du kan bruge til at foretage fejlfinding af problemer under udførelsen. Som nævnt i det tidligere afsnit [filer og mapper](#files-and-directories), tjenesten batchen skriver standard output og standardfejl output til `stdout.txt` og `stderr.txt` filer i mappen opgave på noden Beregn. Du kan bruge portalen Azure eller en af batchen SDK'er for at hente filerne. For eksempel kan du hente disse og andre filer i forbindelse med fejlfinding ved hjælp af [ComputeNode.GetNodeFile] [ net_getfile_node] og [CloudTask.GetNodeFile] [ net_getfile_task] i batchen .NET-biblioteket.

- **Opgave afslutningskoder**

    Som nævnt tidligere, er en opgave markeret som mislykket af tjenesten batchen, hvis den proces, der skal udføres ved opgaven returnerer en undtagen nul afslutningskode. Når en opgave udfører en proces, udfylder batchen opgavens Afslut kode egenskaben med *returnere koden for processen*. Det er vigtigt at være opmærksom på, at en opgaves afslutningskode er **ikke** bestemmes af tjenesten batchen – den bestemmes af processen selve eller operativsystemet, hvor processen udført.

### <a name="accounting-for-task-failures-or-interruptions"></a>Tage højde for følgende mislykkede forsøg på opgave eller afbrydelser

Opgaver kan undertiden mislykkes eller blive afbrudt. Selve programmet opgave kan mislykkes, noden som opgaven kører muligvis genstartes eller noden kan fjernes fra gruppen under en handling af tilpasning af størrelsen Hvis den puljen Deallokeringspolitik er indstillet til at fjerne noder med det samme uden at vente opgaver for at afslutte. I alle tilfælde skal opgaven kan være automatisk sat i kø igen ved at batchen til udførelse af på en anden node.

Du kan også til et forbigående problem som årsag til at hænge eller tager for lang tid at udføre en opgave. Du kan angive den maksimale udførelse af tid for en opgave. Hvis det er overskredet, afbryder batchen programmet opgave.

### <a name="connecting-to-compute-nodes"></a>Oprette forbindelse for at beregne noder

Du kan udføre yderligere fejlfinding og fejlfinding ved at logge på en beregningsnode fra en fjernplacering. Du kan bruge Azure portal til at hente en RDP Remote Desktop Protocol ()-fil for Windows noder og få Secure Shell (SSH) forbindelsesoplysningerne for Linux noder. Du kan også gøre dette ved hjælp af batchen API'er – for eksempel med [Batchen .NET] [ net_rdpfile] eller [Batchen Python](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Hvis du vil oprette forbindelse til en node via RDP eller SSH, skal du først oprette en bruger på noden. For at gøre dette, kan du bruge den Azure portalen, [tilføje en brugerkonto til en node] [ rest_create_user] ved hjælp af batchen REST-API, ringe [ComputeNode.CreateComputeNodeUser] [ net_create_user] metode i batchen .NET eller videoopkald [add_user] [ py_add_user] metode i modulet batchen Python.

### <a name="troubleshooting-bad-compute-nodes"></a>Fejlfinding i forbindelse med "dårlige" beregne noder

I situationer, hvor nogle af dine opgaver mislykkes, kan din batchen klientprogrammet eller service undersøge metadata mislykkedes opgaver til at identificere en eller flere node. Hver node i en gruppe er angivet et entydigt ID, og den node, hvor en opgave kører er inkluderet i opgave-metadata. Når du har identificeret node et problem, kan du udføre flere handlinger med den:

- **Genstart noden** ([REST][rest_reboot] | [.NET][net_reboot])

    Genstart noden kan nogle gange rydde op latente problemer som utilfreds eller gik ned processer. Bemærk, at hvis dine puljen bruger en start-opgave eller tingene bruger en jobopgave forberedelse, udføres de når noden genstartes.

- **Reimage noden** ([REST][rest_reimage] | [.NET][net_reimage])

    Dette geninstalleres operativsystemet på noden. Starte opgaver som med genstarte en node og forberedelse arbejdsopgaver er Kør igen, når noden er blevet reimaged.

- **Fjerne noden fra puljen** ([REST][rest_remove] | [.NET][net_remove])

    Nogle gange er det nødvendigt at helt at fjerne noden fra puljen.

- **Deaktivere Opgaveplanlægning på noden** ([REST][rest_offline] | [.NET][net_offline])

    Dette effektivt tager noden "offline", så ingen yderligere opgaverne tildeles til den, men giver mulighed for noden skal være, der kører og i puljen. Dette gør det muligt at udføre yderligere undersøgelse af årsagen af fejlene i uden at miste mislykkedes opgavens data – og uden noden forårsager yderligere opgave mislykkede forsøg. For eksempel kan du deaktivere Opgaveplanlægning på noden, og derefter [logge på fra en fjernplacering](#connecting-to-compute-nodes) til at undersøge den node hændelseslogfiler eller udføre andre metoder til fejlfinding. Når du er færdig med din undersøgelse, kan du derefter tage noden online igen ved at aktivere opgave planlægning ([RESTEN][rest_online] | [.NET][net_online]), eller udføre én af de andre handlinger, som gennemgået tidligere.

> [AZURE.IMPORTANT] Med hver handling, der er beskrevet i dette afsnit – genstart, reimage, Fjern og Deaktiver opgave planlægning – du kan få til at angive, hvordan opgaver er aktive noden håndteres, når du udfører handlingen. For eksempel, når du deaktiverer opgaveplanlægningen på en node ved hjælp af biblioteket batchen .NET-klienten, kan du angive en [DisableComputeNodeSchedulingOption] [ net_offline_option] optællingsværdi til at angive, om at **afslutte** kører opgaver, **Requeue** dem til planlægning af på andre noder eller tillade kører opgaver for at fuldføre før du udfører handlingen (**TaskCompletion**).

## <a name="next-steps"></a>Næste trin

- Gennemgå en batchen Northwind trinvise i [Introduktion til biblioteket Azure batchen til .NET](batch-dotnet-get-started.md). Der er også en [Python version](batch-python-tutorial.md) af selvstudiet, der kører en arbejdsbyrde på Linux Beregn noder.

- Hente og opbygge [Batchen Explorer] [ github_batchexplorer] eksempel project bruges, mens du udvikle dine batchen løsninger. Brug Stifinder batchen, kan du udføre følgende og meget mere:
  - Overvåge og manipulere grupper, job og opgaver i kontoen batchen
  - Hente `stdout.txt`, `stderr.txt`, og andre filer fra noder
  - Oprette brugere på noder og hente RDP-filer til at logge på remote

- Lær, hvordan du [opretter grupper af Linux Beregn knuder](batch-linux-nodes.md).

- Besøg [Azure batchen forum] [ batch_forum] på MSDN. Forummet er et godt sted at stille spørgsmål, om du blot lærer eller er en ekspert på ved hjælp af batchen.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
