<properties
   pageTitle="Bedste fremgangsmåder til store virksomheder flyttes til Azure | Microsoft Azure"
   description="I denne artikel beskrives et scaffold, som virksomheder kan bruge til at sikre en sikker og administreres miljø."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Azure enterprise scaffold - præskriptive abonnement styring

Virksomheder, der stadig vedtagelse offentlige skyen til dens fleksibilitet og fleksibilitet. De udnytter i skyen styrker for at generere omsætning eller optimere ressourcer for virksomheden. Microsoft Azure leverer en lang række tjenester, kan samle store virksomheder, som dokumentkomponenter til en adresse til et bredt udvalg af arbejdsmængder og programmer. 

Men er det ofte svært at vide, hvor man skal begynde. Når du har besluttet at bruge Azure, opstår nogle spørgsmål, som ofte:

- "Hvordan jeg opfylder vores lovmæssige krav for datasuverænitet i visse lande?"
- "Hvordan jeg sikrer, at nogen ved et uheld ikke ændrer et kritiske system?"
- "Hvordan ved jeg, hvad hver ressource er understøttende, så jeg kan højde for den og det i igen præcist faktura?"

Kundeemne med en tom abonnement med ingen vagt gør er overvældende. Denne tomme plads kan medfører flytningen til Azure.

Denne artikel indeholder et udgangspunkt til teknikere mulighed for at løse behovet for styring og saldo med brug af for fleksibilitet. Det introducerer begrebet en enterprise scaffold, der fører organisationer i implementering og administration af deres Azure abonnementer. 

## <a name="need-for-governance"></a>Har du brug for for at opnå styring

Når du flytter til Azure, skal du løse emnet for styring tidligt at sikre vellykket brug af skyen i virksomheden. Desværre tid og overordnet for oprettelse af et omfattende styring system betyder, at nogle business grupper gå direkte til leverandører uden at involvere enterprise IT. Denne metode kan lade virksomheden åben til svagheder Hvis ressourcerne, der ikke administreres korrekt. Egenskaber for offentlige cloud - fleksibilitet, fleksibilitet og forbrug-baserede priser - er vigtige for business-grupper, der skal hurtigt opfylder kravene til kunder (både intern og ekstern). Men enterprise IT skal sikre, at data og systemer effektivt er beskyttet.

I den virkelige verden bruges stilladser til at oprette basis for strukturen. Scaffold hjælper generelle dispositionen og indeholder anker tidspunkter for mere permanent-systemer for at være tilsluttet. En enterprise scaffold er den samme: et sæt objekter fleksible og Azure-funktioner, som giver struktur til miljø og ankre til tjenester er bygget på offentlige skyen. Den indeholder builders (IT og business grupper) et fundament til at oprette og vedhæfte nye tjenester.

Scaffold er baseret på fremgangsmåder, vi har indsamlet fra mange aftaler med kunder i forskellige størrelser. Disse klienter område fra små organisationer udvikling af løsninger i skyen Fortune 500 virksomheder og uafhængige softwareleverandører, der overfører og udvikling af løsninger i skyen. Virksomhed-scaffold er "specialudviklede" skal være fleksible til at understøtte både traditionelle IT arbejdsmængder og fleksible arbejdsbelastninger; som udviklere, der opretter software-som-en-(SaaS) tjenesteprogrammer baseret på Azure-funktioner.

Virksomhed-scaffold er beregnet på at være grundlaget for hvert nyt abonnement i Azure. Det kan administratorer sikre arbejdsbelastninger, som opfylder minimumkravene styring krav i en organisation uden forhindrer business grupper og udviklere i hurtigt møde deres egne mål.

> [AZURE.IMPORTANT] Styring er afgørende for en vellykket Azure. I denne artikel er beregnet til den tekniske gennemførelse af en enterprise scaffold, men kun rører bredere proces og relationer mellem komponenterne. Politik styring flyder oppefra og ned og afhænger af, hvad virksomheden ønsker at opnå. Naturligvis oprettelsen af en styringsmodel for Azure omfatter repræsentanter fra IT, men vigtigere det bør have stærke repræsentation fra business gruppe ledere og sikkerhed og risikostyring. I slutningen er en virksomhed scaffold om mindske business risikoen for at lette en organisations kommunikations og mål.

Følgende billede beskrives komponenterne i scaffold. Grundlaget er afhængig af en dækkende plan til afdelinger, konti og abonnementer. Søjler består af Ressourcestyring politikker og stærke naming standarder. Resten af scaffold kommer fra core Azure-funktioner og funktioner, der aktiverer et sikkert og administreres miljø.

![scaffold komponenter](./media/resource-manager-subscription-governance/components.png)

> [AZURE.NOTE] Azure er vokset hurtigt siden introduktionen i 2008. Denne vækst påkrævet Microsoft engineering teams for en revision deres metode til at administrere og installation af services. Azure ressourcestyring modellen blev introduceret i 2014 og erstatter det klassiske implementeringsmodel. Ressourcestyring giver organisationer mulighed at nemmere at implementere, organisere og styre Azure ressourcer. Ressourceleder, der indeholder parallelization, når du opretter ressourcer for hurtigere implementering af komplekse, indbyrdes afhængige løsninger. Den indeholder også findelt adgangskontrol og muligheden for at mærke ressourcer med metadata. Microsoft anbefaler, at du opretter alle de ressourcer gennem ressourcestyring modellen. Virksomhed-scaffold er eksplicit udviklet til Ressourcestyring modellen.

## <a name="define-your-hierarchy"></a>Definere dine hierarki

Grundlaget for scaffold er Azure Enterprise tilmelding (og Enterprise Portal). Enterprise tilmelding definerer figuren og brug af Azure tjenester i en virksomhed og er den grundlæggende styring struktur. I enterprise agreement kan kunder yderligere underopdele miljø til afdelinger, konti og til sidst skal abonnementer. Et Azure-abonnement er den grundlæggende enhed, hvor alle ressourcer, der er indeholdt. Den definerer også flere begrænsninger i Azure, som antallet af kerner, ressourcer osv.

![hierarki](./media/resource-manager-subscription-governance/agreement.png)

Hver enkelt virksomhed er anderledes, og hierarkiet i det forrige billede giver betydeligt fleksibilitet i hvordan Azure er organiseret i virksomheden. Før du implementerer den vejledning, der er indeholdt i dette dokument, skal du hierarkiet-modeller og forstå påvirkningen fakturering, ressource access og kompleksitet.

De tre almindelige mønstre for Azure tilmeldinger er:

- **Funktionelle** mønster

    ![funktionelle](./media/resource-manager-subscription-governance/functional.png)

- **Afdeling** mønster 

    ![Business](./media/resource-manager-subscription-governance/business.png)

- **Geografiske** mønster

    ![geografiske](./media/resource-manager-subscription-governance/geographic.png)

Du kan anvende scaffold på niveauet abonnement for at udvide styringen virksomheds krav til abonnementet.

## <a name="naming-standards"></a>Navngive standarder

Den første søjle af scaffold er navngivning af standarder. Flot naming standarder gør det muligt at identificere ressourcer i portalen, på en faktura, og i scripts. Du har sandsynligvis allerede navngive standarder for lokalt infrastruktur. Når du tilføjer Azure i dit miljø, skal du udvide disse naming standarder til din Azure ressourcer. Navngive standard lette mere effektiv styring af miljøet på alle niveauer.

> [AZURE.TIP]
>
> - Gennemse og indføre hvor muligt [mønstre og fremgangsmåder vejledning](./guidance/guidance-naming-conventions.md). Denne vejledning hjælper dig med at fastlægge en sigende naming standard.
> - Du kan bruge camelCasing for af ressourcenavne (såsom myResourceGroup og vnetNetworkName). Bemærk: Der er visse ressourcer, som lager konti, hvor den eneste mulighed er at bruge små bogstaver (og ingen andre specialtegn).
> - Overvej at bruge Azure ressourcestyring politikker (beskrevet i næste afsnit) til at gennemtvinge naming standarder.
 
## <a name="policies-and-auditing"></a>Politikker og overvågning

Den anden søjle af scaffold omfatter oprettelse af [Azure ressourcestyring politikker](resource-manager-policy.md) og [revision aktivitet logfilen](resource-group-audit.md). Ressourcestyring politikker giver dig mulighed for at administrere risikoen i Azure. Du kan angive politikker, der sikrer datasuverænitet ved at begrænse, sikres eller overvågning visse handlinger. 

- Politikken er et standard **tillade** system. Du kan styre handlinger ved at definere og tildele politikker til ressourcer, Afvis eller overvåge handlinger for ressourcer.
- Politikker er beskrevet ved Politikdefinitioner i en politik definition language (if-then betingelser).
- Du opretter politikker med JSON (Javascript Object Notation) formateret filer. Når du definerer en politik, skal du tildele det til et bestemt område: abonnement, ressourcegruppe. eller ressource.

Politikker, der har flere handlinger, som giver mulighed for en detaljerede tilgang til din scenarier. Handlingerne er:

-   **Afvis**: blokerer ressource anmodningen
-   **Overvåg**: gør det muligt for anmodningen, men der tilføjes en linje til loggen aktivitet (som kan bruges til at levere beskeder eller at udløse runbooks)
-   **Føj**: Tilføjer de angivne oplysninger til ressourcen. Hvis der ikke er et "CostCenter" mærke på en ressource, tilføje mærket med en standardværdi.

### <a name="common-uses-of-resource-manager-policies"></a>Almindelige anvendelser af Ressourcestyring politikker

Azure ressourcestyring politikker er et effektivt værktøj i Azure værktøjerne. De gør det muligt at undgå uventede omkostninger, til at identificere en bærer for ressourcer via mærkning og for at sikre, at compliancy krav er opfyldt. Når politikker kombineres med indbyggede overvågningsfunktionerne, kan du fashion komplekse og fleksible løsninger. Politikker for, at virksomheder indeholder kontrolelementer til arbejdsbelastninger, som "Traditionelle IT" og "Agile" arbejdsbelastninger; som udviklingsprogrammer kunde. De mest almindelige mønstre vi se politikker for er:

- **Geografisk-overholdelse/datasuverænitet** - Azure indeholder områder på tværs af hele verden. Virksomheder vil ofte til at styre, hvor ressourcer, der er oprettet (om at sikre, at datasuverænitet eller blot for at sikre ressourcer oprettes tæt slutningen forbrugere af ressourcer).
- **Omkostningsstyring** – en Azure-abonnement kan indeholde ressourcer af mange typer og skalering. Virksomheder vil ofte sikre dig, at standard abonnementer undgå unødvendig høj ressourcer, som kan koste hundredvis af kroner en måned eller mere.
- **Standard styring gennem nødvendige mærker** - kræve mærker er et af de mest almindelige og det ønskede funktioner. Ved hjælp af Azure ressourcestyring politikker er virksomheder i stand til at sikre, at en ressource er korrekt mærket. De mest almindelige mærker, er: afdeling, ressource ejer og miljø type (for eksempel - fremstilling, test, udvikling)

**Eksempler**

"Traditionelle IT" abonnement til line of business programmer

-   Gennemtvinge afdeling og ejer mærker på alle de ressourcer
-   Begræns oprettelse af ressourcer til området Nordamerika
-   Begrænse muligheden for at oprette G-serien FOS og HDInsight klynger

"Fleksible" miljø til en afdeling, oprette skyen programmer

- Tillad oprettelse af ressourcer for at imødekomme data højhedsområde krav, kun i et bestemt område.
- Gennemtvinge miljø mærke på alle de ressourcer. Hvis en ressource er oprettet uden et mærke, føjer den **miljø: Ukendt** mærke til ressourcen.
- Overvågning, når ressourcer, der er oprettet uden for Nordamerika, men forhindrer ikke.
- Overvågning, når der oprettes høj omkostningsressourcer.

> [AZURE.TIP]
>
> Den mest almindelige brug af Ressourcestyring politikker på tværs af organisationer er til at styre, *hvor* ressourcer kan oprettes, og *hvilke* typer ressourcer, der kan oprettes. Ud over at give kontrolelementer i *hvor* og *hvilke*, Brug mange virksomheder politikker til at sikre ressourcer, der har den relevante metadata til at opkræve betaling tilbage til forbrug. Vi anbefaler, at anvende politikker på niveauet abonnement for:
>
> - Geografisk-overholdelse/datasuverænitet
> - Administration af omkostninger
> - Påkrævet mærker (Determined ved forretningsmæssige behov, som BillTo, programmet ejer)
>
> Du kan anvende flere politikker på lavere niveauer af omfang.
 
### <a name="audit---what-happened"></a>Audit -, Hvad skete der?

Hvis du vil se, hvordan dit miljø fungerer, skal du overvåge brugeraktivitet. De fleste ressourcetyper i Azure oprette diagnosticeringslogfiler, kan du analysere via en logfiler værktøjet eller i Azure handlinger Management-pakken. Du kan samle aktivitet logge på tværs af flere abonnementer til at levere en afdelings eller enterprise-visning. Poster for overvågning er både et vigtigt værktøj til diagnosticering og en afgørende metode til at udløse hændelser i Azure-miljø.

Placer aktivitetslogfiler fra ressourcestyring installationer giver dig mulighed at finde ud af de **Handlinger** , der tog, og hvem der har udført. Logfiler over aktivitet kan indsamles og aggregeret ved hjælp af værktøjer som Log analyser.

## <a name="resource-tags"></a>Ressource-mærker

Når brugerne i organisationen tilføjer ressourcer til abonnementet, bliver den grad vigtigt at knytte ressourcer til den relevante afdeling, kunde og -miljø. Du kan vedhæfte metadata til ressourcer gennem [mærker](resource-group-using-tags.md). Du kan bruge mærker til at give oplysninger om ressourcen eller ejeren. Mærker gør det muligt at sammenlægge ikke kun og gruppere ressourcer på forskellige måder, men bruger disse data med henblik på kortejeren. Du kan mærke ressourcer med op til 15 nøgle: værdi-par. 

Ressource mærker er fleksible og skal knyttes til de fleste ressourcer. Eksempler på almindelige ressource koder er:

- BillTo
- Afdeling (eller afdeling)
- Miljø (fremstilling, fase, udvikling)
- Niveau (Web niveau, programmet niveau)
- Programmet ejer
- Projektnavn

![mærker](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Du kan finde flere eksempler på mærker [anbefalede navngivningskonventioner for Azure ressourcer](./guidance/guidance-naming-conventions.md).

> [AZURE.TIP]
>
> Oprette en mærkning strategi, der identificerer på tværs af dine abonnementer metadata, der er behov for business, økonomi, sikkerhed, risikostyring og overordnede styring af miljøet. Overvej at oprette en politik, der sikrer mærkning:
>
> - Ressourcegrupper
> - Lagerplads
> - Virtuelle maskiner
> - Programmet Service miljøer /-servere

## <a name="resource-group"></a>Ressourcegruppe 

Ressourceleder, der gør det muligt at sætte ressourcer i sigende grupper for management, fakturering, naturlige eller. Som tidligere nævnt er Azure to installation-modeller. I tidligere klassisk modellen er den grundlæggende enhed for administration af abonnementet. Det er svært at opdele ressourcer inden for et abonnement, som ført til oprettelse af stort antal abonnementer. Med ressourcestyring modellen så vi indførelsen af grupper. Grupper er objektbeholdere for ressourcer, som er et almindelige livscyklus eller dele en attribut som "alle SQL-servere" eller "Program A".

Grupper kan ikke være indeholdt i hinanden og ressourcer kan kun tilhøre én ressourcegruppe. Du kan anvende visse handlinger på alle ressourcer i en ressourcegruppe. For eksempel fjerner at slette en ressourcegruppe alle ressourcer i ressourcegruppen. Typisk placerer du en hele programmet eller relaterede system i samme ressourcegruppe. Et tre trin program kaldet Contoso webprogram vil for eksempel indeholder webserver, programserver og SQL server på samme ressourcegruppe.

> [AZURE.TIP]
>
> Organisering af dine grupper kan variere fra arbejdsbelastninger, som "Traditionelle IT" til "Fleksible IT" arbejdsmængder
>
> - "Traditionelle IT" arbejdsbelastninger, som er mest almindeligt grupperet efter elementer i det samme livscyklus, som et program. Gruppering af program giver mulighed for individuelle programadministration.
> - "Fleksible IT" arbejdsbelastninger tendens til at fokusere på eksterne med kundekontakt skyen programmer. Ressourcegruppe skal afspejle lag af installation (såsom Web lag, App niveau) og administration.

## <a name="role-based-access-control"></a>Rollebaseret adgangskontrol

Du stiller sandsynligvis dig selv "der bør have adgang til ressourcer?" og "Hvordan styrer jeg denne adgang?" Tillade eller ikke tillade adgang til portalen Azure og kontrollere adgangen til ressourcer i portalen er afgørende. 

Når Azure oprindeligt blev udgivet, adgang til kontrolelementer til et abonnement er grundlæggende: webstedsadministrator eller samtidig Administrator. Få adgang til et abonnement i klassisk model implicit adgang til alle ressourcerne på portalen. Denne funktion ikke detaljerede kontrolelement ført til udbredelsen af abonnementer til at levere et niveau af begrundet adgangskontrol til en Azure tilmelding.

Denne spredning af abonnementer ikke længere er nødvendig. Med rollebaseret adgangskontrol, kan du tildele brugere til standardroller (såsom almindelige "læser" og "writer" typer af roller). Du kan også angive brugerdefinerede roller.

> [AZURE.TIP]
>  
> - Forbind din virksomhedens identitet store (mest almindelige Active Directory) til Azure Active Directory ved hjælp af værktøjet AD-forbindelse.
> - Styre administrator/Co-administratoren af et abonnement med en administreret identitet. Co-administrator-administrator tildele **ikke** til ejeren af et nyt abonnement. I stedet bruge RBAC roller for at give **ejeren** rettigheder til en gruppe eller en enkelt.
> - Føje Azure brugere til en gruppe (for eksempel programmet X ejere) i Active Directory. Brug gruppen synkroniserede skal være medlemmerne af de nødvendige rettigheder til at administrere ressourcegruppen, der indeholder programmet.
> - Følg princippet bag give **minimale privilegier** , der kræves til at gøre det forventede arbejde. Eksempel:
>     - Installation gruppe: En gruppe, som kun kan installere ressourcer.
>     - Virtuelt administration: En gruppe, der kan genstart FOS (for handlinger)

## <a name="azure-resource-locks"></a>Azure ressourcelåse

Som din organisation føjer core services til abonnementet, bliver den grad vigtigt at sikre, at disse tjenester er tilgængelige for at undgå afbrydelser business. [Ressourcelåse](resource-group-lock-resources.md) gør det muligt at begrænse handlinger på værdiskabende ressourcer, hvor redigering eller sletning af dem ville have stor indflydelse på dine programmer eller -skyinfrastruktur. Du kan anvende låse til et abonnement, ressourcegruppe eller ressource. Normalt anvender du låse til at finde grundlæggende ressourcer som virtuelle netværk, gateways og lagerplads konti. 

Ressourcelåse understøtter to værdier: CanNotDelete og skrivebeskyttet. CanNotDelete betyder, at brugere (med de nødvendige rettigheder) kan stadig læse eller redigere en ressource, men kan ikke slette den. Skrivebeskyttet betyder, at autoriserede brugere ikke kan slette eller ændre en ressource.

Hvis du vil oprette eller slette management låse, skal du have adgang til `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` handlinger.
Af de indbyggede roller tildeles kun ejeren og Access Brugeradministrator disse handlinger.

> [AZURE.TIP] Core netværksindstillinger skal være beskyttet med låse. Utilsigtet sletning af en gateway til websted VPN ville være uoverskuelige et Azure-abonnement. Azure tillader ikke, at du sletter et virtuelt netværk, der er i brug, men anvende flere begrænsninger er en praktisk sikkerheds. Politikker er også afgørende til vedligeholdelse af relevante kontrolelementer. Vi anbefaler, at du anvender en **CanNotDelete** Lås til politikker, der er i brug.
>
> - Virtuelt netværk: CanNotDelete
> - Netværk sikkerhedsgruppe: CanNotDelete
> - Politikker: CanNotDelete

## <a name="core-networking-resources"></a>Core netværk ressourcer

Adgang til ressourcer, der kan være enten interne (inden for virksomhedens netværk) eller eksterne (via internettet). Det er nemt for brugere i organisationen at placere ved et uheld ressourcer i det forkerte sted, og åbne dem potentielt skadelig Access. Som med lokalt enheder skal virksomheder føje relevante kontrolelementer for at sikre, at Azure brugere foretager de rigtige beslutninger. For at opnå styring abonnement gennemgås core ressourcer, der giver grundlæggende styring af access. Ressourcerne, der core består af:

- **Virtuel netværk** er objektbeholder objekter for undernet. Selvom ikke absolut nødvendigt, er det ofte bruges ved tilslutning af programmer til interne virksomhedens ressourcer.
- **Netværk sikkerhedsgrupper** ligner en firewall og angive regler til hvor en ressource kan "tale" via netværket. De giver detaljerede kontrol over, hvordan / Hvis en undernet (eller virtuelt) kan oprette forbindelse til internettet eller andre undernet i det samme virtuelle netværk.

![Core netværk](./media/resource-manager-subscription-governance/core-network.png)

> [AZURE.TIP]
>  
> - Oprette virtuelle netværk dedikeret til eksterne arbejdsmængder og interne arbejdsmængder. Denne metode mindsker risikoen for ved et uheld placere virtuelle maskiner, der er beregnet til interne arbejdsmængder på en ekstern modstående plads.
> - Netværk sikkerhedsgrupper er vigtige for denne konfiguration. Bloker adgang til internettet fra interne virtuelle netværk som minimum, og Bloker adgang til virksomhedens netværk fra eksterne virtuelle netværk.

### <a name="automation"></a>Automatisering

Administrere ressourcer individuelt er begge tidskrævende og potentielt fejl giver risiko til bestemte handlinger. Azure leverer forskellige automatisering-funktioner, herunder Azure automatisering, logik Apps og Azure funktioner. [Azure automatisering](./automation/automation-intro.md) kan administratorer til at oprette og definere runbooks for at håndtere almindelige opgaver i administration af ressourcer. Du kan oprette runbooks ved hjælp af en PowerShell-Kodeeditor eller en grafisk editor. Du kan oprette komplekse med flere faser arbejdsprocesser. Azure automatisering bruges ofte til at håndtere fælles opgaver som lukke ubrugte ressourcer eller oprettelse af ressourcer i svar til en bestemt udløser uden brug af mennesker brugerinput.

> [AZURE.TIP]
>
> - Oprette en Azure automatiske konto og gennemse de tilgængelige runbooks (både grafiske og kommandoen linje) der er tilgængelige i [Galleriet Runbook](./automation/automation-runbook-gallery.md).
> - Importere og tilpasse vigtige runbooks til egen brug.
>
> Et almindeligt scenarie er muligheden for at Start/Lukning virtuelle maskiner på en tidsplan. Der findes runbooks for eksempel, der er tilgængelige i galleriet, der både håndtere dette scenario og lære at udvide den.


## <a name="azure-security-center"></a>Azure Security Center

Måske har en af de største blokering til skyen indføring været problemstillinger over sikkerhed. IT risikoen ledere og sikkerhed afdelinger skal sikre dig, at ressourcer i Azure er sikre. 

[Azure Sikkerhedscenter](./security-center/security-center-intro.md) giver en central visning af sikkerhedsstatus for ressourcer i abonnementerne, og anbefalinger, at forhindre kompromitteret ressourcer. Det kan gøre mere detaljerede politikker (for eksempel anvender politikker til bestemte ressourcegrupper, som giver mulighed for at skræddersy deres stilling til de håndtere risikoen). Endelig er Azure Security Center en åben platform, som gør det muligt for Microsoft-partnere og uafhængige softwareleverandører for at oprette software, der tilsluttes Azure Security Center til at forbedre dens egenskaber. 

> [AZURE.TIP]
>
> Azure Sikkerhedscenter er aktiveret som standard i hvert abonnement. Skal du aktivere dataindsamling fra virtuelle maskiner tillade Azure Sikkerhedscenter til at installere webstedets agent og begynde at indsamle data.
>
> ![dataindsamling](./media/resource-manager-subscription-governance/data-collection.png)

## <a name="next-steps"></a>Næste trin

- Nu hvor du har lært om abonnement styring, er det tid at få vist disse anbefalinger i praksis. Se [eksempler på implementere Azure abonnement styring](resource-manager-subscription-examples.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) bidraget til dette emne.*
