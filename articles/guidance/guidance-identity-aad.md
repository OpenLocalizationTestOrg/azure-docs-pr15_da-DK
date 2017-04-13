<properties
   pageTitle="Implementering af Azure Active Directory | Microsoft Azure"
   description="Sådan implementere en sikker hybrid netværksarkitektur, ved hjælp af Azure Active Directory."
   services="guidance,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="telmos"/>

# <a name="implementing-azure-active-directory"></a>Implementering af Azure Active Directory

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskriver bedste fremgangsmåder til integrering af lokale Active Directory (AD) domæner og områder med Azure Active Directory til at levere skybaseret identitetsgodkendelse.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne referencearkitektur bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

Du kan bruge directory og -identitet tjenester, som dem, der leveres af AD katalogtjenester (AD DS) til at godkende identiteter. Disse identiteter kan være medlem af brugere, computere, programmer eller andre ressourcer, som indgår i rammen sikkerhed. Du kan hoste directory og identitet services i det lokale miljø, men i et hybridt scenarie, hvor elementer i et program er placeret i Azure kan det være mere effektivt at udvide denne funktionalitet i skyen. Denne metode kan hjælpe med at reducere ventetid forårsaget ved at sende godkendelse og lokale godkendelse af anmodninger om fra skyen tilbage til de mappe og identitet services kører i det lokale miljø.

Azure findes to løsninger for implementere directory og -identitet tjenester i skyen:

1. Du kan bruge [Azure Active Directory (AAD)] [ azure-active-directory] til at oprette en AD-domæne i skyen og knytte den til et lokalt AD-domæne. AAD gør det muligt at konfigurere single sign-on (SSO) til brugere, der kører programmer, der er åbnet via skyen. AAD anvender [Azure AD-forbindelse] [ azure-ad-connect] kører lokalt at gentage objekter og identiteter opbevares i det lokale lager til skyen.

    Du kan også implementere AAD uden at bruge en lokal adresseliste. I dette tilfælde fungerer AAD som den primære kilde til alle oplysninger om identitet i stedet for med data replikeres fra en lokal adresseliste.

    Bemærk, at mappen i skyen er **ikke** en udvidelse af en lokal adresseliste. I stedet er det en kopi, der indeholder de samme objekter og identiteter. Ændringer af disse elementer i det lokale miljø, kopieres til skyen, men ændringer i skyen **er ikke** replikeres tilbage til det lokale domæne.

    >[AZURE.NOTE] Azure Active Directory Premium understøtter tilbageskrivning af brugernes adgangskoder, så brugerne i det lokale miljø til at udføre selvbetjening nulstilling af adgangskoder i skyen. Dette er de eneste oplysninger, der AAD synkroniserer tilbage til den lokale mappe. Du kan finde flere oplysninger om de forskellige udgaver af AAD og deres funktioner se [Azure Active Directory udgaver][aad-editions].

2. Du kan implementere en VM kører AD DS som et domænenavn fra domænecontrolleren i Azure, hvis du vil udvide din eksisterende AD-infrastruktur (herunder Active Directory-Domænetjenester og AD FS) fra dit lokale datacenter. Denne metode er mest almindelige scenarier, hvor de lokale netværk og Azure virtuelt netværk er forbundet via en VPN-og/eller ExpressRoute forbindelse. Denne løsning understøtter også tovejs-gentagelse, så foretage ændringer i skyen og lokalt, skal det sted, hvor det er mest relevante.

Denne arkitektur fokuserer på løsning 1. Finde flere oplysninger om den anden løsning, i [Forlænge Active Directory til Azure][guidance-adds].

Typisk anvendelse sager for denne arkitektur omfatter:

- Give SSO for slutbrugere kører SaaS og andre programmer i skyen, med de samme legitimationsoplysninger, de har angivet for lokale programmer.

- Publicering lokale webprogrammer via skyen for at give adgang til eksterne brugere, der hører til din organisation.

- Implementere selvbetjeningsfunktionerne for slutbrugere, såsom nulstilling af deres adgangskoder og uddelegere gruppeadministration. 

    >[AZURE.NOTE] Disse funktioner kan styres af en administrator via Gruppepolitik.

- Situationer, hvor de lokale netværk og Azure virtuelt netværk, der er vært for skyen programmer ikke er direkte knyttet ved hjælp af en VPN-tunnel eller ExpressRoute kredsløb.

Bemærk, at AAD ikke giver alle funktioner i AD. For eksempel håndterer AAD i øjeblikket kun brugergodkendelse i stedet for computergodkendelse. Nogle programmer og tjenester, såsom SQL Server, kan kræve computergodkendelse i så fald denne løsning ikke er relevant. Desuden AAD muligvis ikke passer til systemer, hvor komponenter kan flyttes på tværs af grænsen på-lokale/skyen i stedet for kun der kopieres.

> [AZURE.NOTE] Finde en mere detaljeret beskrivelse af, hvordan Azure Active Directory fungerer, kan du se [Microsoft Active Directory forklaring][aad-explained].

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur. Du kan finde flere oplysninger om elementerne, arbejdsbelastningen i Azure læse [Kører FOS for en N-delt arkitektur på Azure][implementing-a-multi-tier-architecture-on-Azure]:

> [AZURE.NOTE] Enkel, dette diagram kun viser de forbindelser, der er direkte relateret til AAD, og ikke afbilde webbrowser anmodning omdirigeringer eller andre protokol relaterede trafik, der kan forekomme som en del af processen godkendelse og identitet sammenslutning. For eksempel en bruger (lokale eller remote) typisk adgang til en WebApp via en browser, og WebApp kan transparent omdirigere webbrowseren for at godkende anmodningen via AAD. Når godkendt, kan anmodningen sendes tilbage til WebApp sammen med de relevante identitetsoplysninger.

[! [0]][0]

- **Azure Active Directory-lejer**. Dette er en forekomst af AAD, der er oprettet af din organisation. Det fungerer som en simpel katalogtjeneste til skyen programmer (den indeholder objekter, der er kopieret fra lokale AD), og leverer identitet tjenester.

- **Web niveau undernet**. Dette undernet indeholder VM'er, der implementerer et brugerdefineret skybaseret program udviklet af organisationen og til som AAD kan fungere som en identitet broker.

- **Lokale Active Directory-Domænetjenester server**. Din organisation har allerede sandsynligvis en eksisterende katalogtjenesten såsom AD DS. Du kan synkronisere mange af elementerne i mappen AD DS (som bruger og gruppeoplysninger) med AAD til at aktivere AAD til at bruge disse oplysninger til at godkende identiteter.

- **Azure AD-forbindelse Synkroniser server**. Dette er en lokal computer, der kører tjenesten Azure AD-forbindelse synkronisering. Du kan installere denne tjeneste ved hjælp af softwaren Azure AD-forbindelse. Azure AD-forbindelse Synkroniser tjeneste synkroniserer information (brugere, grupper, kontakter, osv.), der findes i lokale AD til AAD i skyen. For eksempel kan du klargør eller deprovision grupper og brugere lokalt og ændringerne er blevet overført til AAD. Tjenesten Azure AD-forbindelse sync overfører oplysninger til AAD lejer.

    >[AZURE.NOTE] Af sikkerhedsmæssige årsager gemmes brugerens adgangskoder ikke direkte i AAD. I stedet indeholder AAD en hash hver adgangskode. Dette er tilstrækkelige til at bekræfte en brugers adgangskode. Hvis en bruger kræver en nulstilling af adgangskode, kan det skal være udført lokalt og giver nye resultat, der er sendt til AAD. AAD Premium-udgaver inkluderer funktioner, som kan automatisere denne opgave for at aktivere brugere til at nulstille deres egne adgangskoder.

## <a name="recommendations"></a>Anbefalinger

Dette afsnit indeholder en oversigt over anbefalinger til implementering af AAD på følgende måde.

- AD Connect
- Sikkerhed

### <a name="azure-ad-connect-sync-service-recommendations"></a>Azure AD-forbindelse Synkroniser service anbefalinger

Synkronisering er bekymrede for at sikre, at brugere identitetsoplysninger i skyen er i overensstemmelse med, der afholdes på det lokale system. Formålet med tjenesten Azure AD-forbindelse synkronisering er at vedligeholde denne konsistens. Følgende punkter opsummering anbefalinger til implementering af tjenesten Azure AD-forbindelse synkronisering:

- Før du implementerer Azure AD-forbindelse synkronisering, skal du bestemme synkronisering kravene i din organisation (hvad der skal synkroniseres fra hvilke domæner, og hvor ofte. I artiklen [fastslå directory-synkronisering krav] [ aad-sync-requirements] beskrives de punkter, du bør overveje.

- Du kan køre Azure AD-forbindelse synkroniseringstjenesten ved hjælp af en VM eller en computer hostet i det lokale miljø. Afhængigt af svingende oplysningerne i adresselisten AD, når den indledende synkronisering med AAD er blevet udført sandsynligvis belastning på tjenesten Azure AD-forbindelse sync ikke kunne høj. Ved hjælp af en VM gør det muligt at skalere nemmere server (skærm aktiviteten, som beskrevet i afsnittet [overvågnings overvejelser i forbindelse med](#monitoring-considerations) at finde ud af, om det er nødvendigt).

- Hvis du har flere domæner i det lokale miljø i et område, du kan gemme og synkronisere oplysninger til hele området til en enkelt AAD lejer (dette er den anbefalede tilgang). Filtrere oplysninger til identiteter, der forekommer i mere end ét domæne, så hver identitet vises kun én gang i AAD i stedet for at blive dubleret, da det kan føre til uoverensstemmelser, når dataene er synkroniseret. Denne fremgangsmåde kræver, at implementere flere Azure AD-forbindelse Synkroniser servere. Du kan finde yderligere oplysninger finder scenariet flere AAD i sektionen [topologi overvejelser i forbindelse med](#topology-considerations) . 

- Bruge filtrering til at begrænse de data, der er gemt i AAD til dem, som er nødvendig. Din organisation måske eksempelvis ikke gemme oplysninger om inaktiv eller ikke personlige konti i AAD. Filtrering kan være gruppe-baserede, domæne-baserede, OU-baserede eller attribut-baseret, og du kan kombinere filtre for at oprette mere komplekse regler. For eksempel kan du vælge for at synkronisere kun objekter i et domæne, der har en bestemt værdi i en valgt attribut. Du kan finde detaljerede oplysninger i [Azure AD-forbindelse synkronisering: konfigurere filtrering][aad-filtering].

- Køre en sekundær midlertidig server for at implementere høj tilgængelighed for tjenesten AD-forbindelse synkronisering. Yderligere oplysninger finder du i afsnittet [topologi overvejelser i forbindelse med](#topology-considerations) .

### <a name="security-recommendations"></a>Anbefalinger til sikkerhed

Følgende elementer opsummering de primære sikkerhedsanbefalinger til implementering af AAD, afhængigt af organisationens krav:

- Administrere brugeres adgangskoder.

    Hvis du bruger en premium udgave af AAD, kan du aktivere adgangskode tilbageskrivning fra AAD til din lokale mappe ved at udføre en brugerdefineret installation af Azure AD-forbindelse:

    [! [9]][9]

    Denne funktion gør det muligt for brugerne at nulstille deres egne adgangskoder fra i portalen Azure, men bør kun aktiveres, når du har gennemgået din organisations adgangskodepolitik sikkerhed. For eksempel du kan begrænse, hvilke brugere kan ændre deres adgangskode, og du kan tilpasse den adgangskode management oplevelse. Kan finde flere oplysninger i [Tilpasse administration af adgangskoder til din organisations behov][aad-password-management].

- Vedligeholde beskyttelse for lokale programmer, der kan få adgang til eksternt.

    Brug Azure AD-proxyen til at give kontrolleret adgang til lokale webprogrammer fra eksterne brugere via AAD. Denne metode beskytter dine programmer ved at eksponere dem ikke direkte til internettet. Kun brugere, der har gyldige legitimationsoplysninger i mappen Azure har adgang til dine programmer. Kan finde flere oplysninger i artiklen [Aktivere proxyen på portalen Azure][aad-application-proxy].

- Aktivt overvåger AAD for tegn på mistænkelig aktivitet.

    Overvej at bruge AAD Premium P2 edition, som omfatter AAD identitetsbeskyttelse. Beskyttelse af din identitet bruger algoritmer, der er tilpasset maskine læring og heuristik til at registrere afvigelser og offentliggjort hændelser, der kan betyde, at en identitet er blevet afsløret. Det kan for eksempel registrerer potentielt uoverensstemmende aktivitet som uregelmæssige logon aktiviteter, logon fra ukendte kilder eller IP-adresser med mistænkelige aktiviteter eller logon fra enheder, der kan være inficeret. Beskyttelse af din identitet genererer ved hjælp af disse data rapporter og beskeder, der gør det muligt at undersøge disse risikoen begivenheder og tage relevante afhjælpning eller afhjælpning handling. Du kan finde flere oplysninger, se [Azure Active Directory identitetsbeskyttelse][aad-identity-protection].

    Du kan også bruge funktionen rapportering af AAD i portalen Azure til at overvåge mistænkelige og andre sikkerhedsrelaterede aktiviteter i systemet til virkelighed. AAD kan oprette en række oversigt rapporter:

    [! [17]][17]

    Kan finde flere oplysninger om brug af disse rapporter i [Azure Active Directory rapportering Guide][aad-reporting-guide].

## <a name="topology-considerations"></a>Overvejelser om topologi

Hvis du integrerer en lokal adresseliste med AAD, kan du konfigurere Azure AD-forbindelse for at implementere en søgetopologien, der passer bedst til din organisations krav. Topologier, der understøtter Azure AD-forbindelse omfatter følgende:

- **Enkelt område, enkelt AAD mappe**. I denne topologi, du bruger Azure AD-forbindelse til at synkronisere objekter og identitetsoplysninger i en eller flere domæner i en enkelt lokalt område med en enkelt AAD lejer. Dette er standard topologien implementeret af udtrykkelig installationen af Azure AD-forbindelse.

    [! [1]][1]

    Ikke oprette flere Azure AD-forbindelse Synkroniser servere for at forbinde forskellige domæner i det samme lokale skov til samme AAD lejer, medmindre du kører en server til synkronisering af Azure AD-forbindelse i arrangere tilstand (se arrangere Server).

- **Flere områder, enkelt AAD mappe**. Brug denne topologi, hvis du har mere end én lokale skov. Du kan konsolidere identitetsoplysninger, så hver entydig bruger repræsenteres én gang i mappen AAD, selv hvis den samme bruger findes i mere end én skov. Alle områder Brug samme Azure AD-forbindelse Synkroniser server. Azure AD-forbindelse Synkroniser server behøver ikke at være en del af et domæne, men det skal være tilgængelig fra alle områder:

    [! [2]][2]

    I denne topologi ikke bruge separat Azure AD-forbindelse synkronisere-servere at forbinde hvert lokalt område til en enkelt AAD lejer. Dette kan medføre dublerede identitetsoplysninger i AAD, hvis der findes i mere end én skov brugere.

- **Flere områder, separat topologier**. Denne fremgangsmåde gør det muligt at flette identitetsoplysninger fra separate områder på en enkelt AAD lejer. Denne strategi er nyttig, hvis du kombinerer områder fra forskellige organisationer (efter fusion eller virksomhedskøb, for eksempel), og oplysningerne om identitet for hver bruger holdes i kun én skov:

    Hvis GALs i hvert område er synkroniseret, kan en bruger i én skov være findes i en anden som en kontakt. Dette kan ske, hvis din organisation er for eksempel implementeret GALSync i Forefront identitet manager 2010 eller Microsoft identitet Manager 2016. I dette scenario, kan du angive, at brugere skal identificeres ved deres *Mail* -attributten. Du kan også sammenligne identiteter ved hjælp af attributterne *ObjectSID* og *msExchMasterAccountSID* . Dette er nyttigt, hvis du har en eller flere ressource skov med deaktiverede konti.

- **Arrangere server**. I denne konfiguration kører du en anden forekomst af Azure AD-forbindelse Synkroniser serveren parallelt med først. Denne struktur understøtter scenarier f.eks.:

    - Høj tilgængelighed.

    - Teste og installere en ny konfiguration af synkronisering Azure AD-forbindelse.

    - Introduktion til en ny server og udfase en gamle konfiguration. 

    I disse scenarier kører den anden forekomst i *arrangere tilstand*. Serveren registrerer importerede objekter og synkronisering af data i databasen, men overfører ikke data til AAD. Kun, når du deaktiverer midlertidige tilstand serveren Begynd at skrive data til AAD, og start ydeevne adgangskode Skriv-bagsider i mapperne lokale eventuelt:

    [! [4]][4]

    Kan finde flere oplysninger under [Azure AD-forbindelse synkronisering: driftsopgaver og overvejelser i forbindelse med][aad-connect-sync-operational-tasks].

- **Flere AAD mapper**. Det anbefales, at du opretter en enkelt AAD mappe til en organisation, men der kan være situationer, hvor du skal partitionsoplysninger på tværs af adskilte AAD mapper. I dette tilfælde skal du sikre dig, at de enkelte objekter fra den lokale skov vises kun i én AAD mappe for at undgå problemer med synkronisering og adgangskode tilbageskrivning.

    Konfigurere separat Azure AD-forbindelse for at implementere dette scenario, synkronisere servere for hver AAD mappe, og brug filtrering, så hver Azure AD-forbindelse Synkroniser server fungerer på et udelukker sæt objekter: 

    [! [5]][5]

Se [topologier til Azure AD-forbindelse]kan finde flere oplysninger om disse topologier[aad-topologies].

## <a name="user-sign-in-considerations"></a>Bruger-logon overvejelser

Tjenesten AAD antages det, at brugere logger på ved at give den samme adgangskode, de bruger i det lokale miljø, og Azure AD-forbindelse Synkroniser serveren konfigurerer synkronisering af adgangskoder mellem det lokale domæne og AAD som standard. Dette er relevant for mange organisationer, men du bør overveje organisationens eksisterende politikker og infrastruktur. Eksempel:

- Sikkerhedspolitikken for din organisation kan forhindre synkronisering af adgangskode hashes til skyen.

- Du kan kræve, at brugerne opleve problemfri SSO (uden yderligere adgangskode beder), når adgang til skyen ressourcer fra domæne joinforbundne maskiner på virksomhedens netværk.

- Din organisation muligvis allerede ADFS eller en tredjepartsforhandler sammenslutning leverandør installeret. Du kan konfigurere AAD for at bruge denne infrastruktur til at implementere godkendelse og SSO i stedet for ved hjælp af adgangskodeoplysningerne i skyen.

Du kan finde flere oplysninger, se [Azure AD forbinde bruger Sign-on indstillinger][aad-user-sign-in].

## <a name="azure-ad-application-proxy-considerations"></a>Azure AD-program-proxy overvejelser

Brug Azure AD til at give adgang til lokale programmer.

- Få vist dine lokale webprogrammer ved hjælp af proxyen forbindelser administrere ved komponenten Azure AD-program-proxy. Programmet proxy forbindelsen åbnes en udgående netværksforbindelse til Azure AD-proxyen. Anmodninger om eksterne brugere, der er distribueret tilbage fra AAD via denne forbindelse til webapps. Denne funktion fjerner behovet for at åbne indgående porte i lokal firewall, reduceres angrebsoverfladen, der vises af din organisation.

Kan finde flere oplysninger under [Udgiv programmer, der bruger Azure AD proxyen][aad-application-proxy].

## <a name="object-synchronization-considerations"></a>Overvejelser i forbindelse med objekt synkronisering

Standardkonfigurationen af Azure AD-forbindelse synkroniserer objekter fra din lokale AD-mappe, der er baseret på sæt regler, der er angivet i artiklen [Azure AD-forbindelse synkronisering: forstå standardkonfiguration][aad-connect-sync-default-rules]. Kun objekter, der opfylder disse regler synkroniseres, andre ignoreres. For eksempel brugerobjekter skal have en entydig *sourceAnchor* attribut og attributten *accounEnabled* skal udfyldes. Brugerobjekter, der ikke har en *sAMAccountName* -attribut eller, der starter med teksten, *AAD_* eller *MSOL_* synkroniseres ikke. Azure AD-forbindelse gælder flere regler for brugeren, kontakt, gruppen, ForeignSecurityPrincipal og Computer objekter. Hvis du vil ændre standardsættet af regler skal du bruge redigeringsprogrammet synkronisering regler, der installeres med Azure AD-forbindelse (også beskrevet i [Azure AD-forbindelse synkronisering: forstå standardkonfiguration][aad-connect-sync-default-rules]).

Du kan definere dine egne filtre for at begrænse objekterne, der skal synkroniseres af domæne eller OU. Eller implementere mere komplekse brugerdefinerede filtrering, som beskrevet i [Azure AD-forbindelse synkronisering: konfigurere filtrering][aad-filtering].

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Bruge betinget adgangskontrol til at afvise anmodninger om godkendelse fra uventede kilder:

- Udløse [Azure Multi-Factor Authentication (MFA)] [ azure-multifactor-authentication] Hvis en bruger forsøger at oprette forbindelse fra en placering, der ikke er tillid til (såsom på tværs af internettet) i stedet for et netværk, der er tillid til.

- Brug platform enhedstype på brugeren (iOS, Android, Windows Mobile Windows) til at finde ud af access-politik til programmer og funktioner.

- Registrere aktiverede/deaktiverede tilstanden af brugernes enheder, og inkorporere disse oplysninger i politik adgangskontrol. Eksempelvis hvis en bruger telefon går tabt eller stjæles bør det registreres som deaktiveret for at forhindre, at det kan bruges til at få adgang.

- Styre adgangsniveauet til en bruger, der er baseret på gruppemedlemskab. Brug [regler for dynamisk medlemskab AAD] [ aad-dynamic-membership-rules] til at forenkle administration af gruppe. En kort oversigt over, hvordan dette virker, skal du se [Introduktion til dynamiske medlemskaber for grupper][aad-dynamic-memberships].

- Bruge betinget access risikoen politikker med AAD identitetsbeskyttelse til avanceret beskyttelse baseret på usædvanlige logon aktiviteter eller andre begivenheder.

Få mere at vide under [Azure Active Directory betinget adgang][aad-conditional-access].

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Skalerbarhed er behandlet i tjenesten AAD og konfiguration af Azure AD-forbindelse synkronisering:

- Du har ikke konfigurere eventuelle indstillinger til at implementere skalerbarhed for tjenesten AAD. Tjenesten AAD understøtter skalerbarhed baseret på replikaer. AAD implementerer et enkelt primære replika, hvilke håndtag skrive handlinger, og flere skrivebeskyttet sekundær replikaer. AAD omdirigerer transparent forsøgte skriver foretaget mod sekundær replikaer til den primære replika. AAD indeholder eventuel konsistens. Alle ændringer i den primære replika overføres til de sekundære replikaer. Da de fleste handlinger mod AAD er læser i stedet for skrivning, skaleres denne arkitektur godt.

    Du kan finde flere oplysninger, se [Azure AD: struktur af vores geografisk overflødige, høj tilgængelighed, fordelt skyen directory][aad-scalability].

- Til Azure AD-forbindelse Synkroniser server, skal du se antallet af objekter, du sandsynligvis at synkronisere fra din lokale mappe. Hvis du har mindre end 100.000 objekter, kan du bruge den SQL Server Express LocalDB software, der er angivet med Azure AD-forbindelse. Hvis du har et større antal objekter, skal du installere en version af SQL Server og udføre en brugerdefineret installation af Azure AD-forbindelse, der angiver, at den skal bruge en eksisterende forekomst af SQL Server.

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Som med skalerbarhed problemstillinger tilgængelighed strækker sig over tjenesten AAD og konfigurationen af Azure AD-forbindelse:

- Tjenesten AAD er udviklet til at levere høj tilgængelighed. Der er ingen kan konfigureres af brugeren tilgængelighed indstillinger. Det er distribueret geografisk og kører i flere data Centrerer opslag over hele verden med automatiseret failover. Hvis et datacenter er tilgængelig, sikrer AAD, at dataene directory er tilgængelige for forekomst adgang i mindst to mere regionalt spredt datacentre.

    >[AZURE.NOTE] SERVICENIVEAUAFTALE for AAD grundlæggende Premium services garantier mindst 99,9% tilgængelighed og. Der er ingen SERVICENIVEAUAFTALE for det gratis niveau i AAD. Du kan finde flere oplysninger, se [SERVICENIVEAUAFTALE til Azure Active Directory][sla-aad].

- Du kan køre en anden forekomst i arrangere tilstand, for at øge tilgængeligheden af Azure AD-forbindelse Synkroniser server som beskrevet i sektionen [topologi overvejelser i forbindelse med](#topology-considerations) . 

    Desuden, hvis du ikke bruger den SQL Server Express LocalDB-forekomst, der følger med Azure AD-forbindelse, skal du overveje høj tilgængelighed til SQL Server. Bemærk, at kun høj tilgængelighed løsningen understøttes SQL-klynge. Løsninger som spejling og altid på der ikke understøttes af Azure AD-forbindelse.

    Du kan finde yderligere overvejelser om vedligeholdelse af tilgængeligheden af Azure AD-forbindelse Synkroniser server, og hvordan du kan gendanne efter en fejl under [Azure AD-forbindelse synkronisering: driftsopgaver og overvejelser i forbindelse med - nedbrud][aad-sync-disaster-recovery].

## <a name="management-considerations"></a>Overvejelser i forbindelse med administration

Der er to aspekter til administration af AAD:

1. Administrere AAD i skyen.

2. Vedligeholde Azure AD-forbindelse Synkroniser servere.

AAD indeholder følgende indstillinger for administration af domæner og mapper i skyen:

- [Azure Active Directory PowerShell-modulet][aad-powershell]. Brug dette modul, hvis du har brug at oprette et script almindelige Azure AD administrative opgaver såsom brugeradministration, domæneadministration og til at konfigurere Enkeltlogon.

- Azure AD-management-blade i portalen Azure. Denne blade giver en interaktiv management visning af mappen, og gør det muligt at styre og konfigurere de fleste aspekter af AAD.

    [! [10]][10]

Azure AD-forbindelse installationer følgende værktøjer, som du kan bruge til at vedligeholde synkroniseringstjenester Azure AD-forbindelse fra din lokale computere:

- Microsoft Azure Active Directory Connect konsollen. Dette værktøj gør det muligt at ændre konfiguration af Azure Active Directory-synkronisering, tilpasse, hvordan synkronisering sker, aktivere eller deaktivere midlertidige tilstand og skifte bruger logon tilstand (du kan aktivere AD FS-logon ved hjælp af din lokale infrastruktur).

- Synkronisering Service Manager. Brug fanen *Handlinger* i dette værktøj til at administrere processen til synkronisering og registrere om dele af processen har mislykkedes. Du kan udløse synkroniseringen manuelt ved hjælp af dette værktøj. 

    [! [12]][12]

    Fanen *forbindelser* gør det muligt at styring af forbindelser til domænerne (lokalt og i skyen) som synkronisering program er tilknyttet:

    [! [13]][13]

-  Synkronisering regler Editor. Brug dette værktøj til at tilpasse den måde, objekter er ændret, når de er kopieret mellem en lokal adresseliste og AAD i skyen. Dette værktøj gør det muligt at angive yderligere attributter og objekter til synkronisering, og filtre til at bestemme, hvilke forekomster skal eller skal ikke synkroniseres.

    Du kan finde flere oplysninger i afsnittet synkronisering regel Editor i dokumentet [Azure AD-forbindelse synkronisering: forstå standardkonfiguration][aad-connect-sync-default-rules].

Siden [Azure AD-forbindelse synkronisering: bedste fremgangsmåder til at ændre standardkonfigurationen] [ aad-sync-best-practices] indeholder flere oplysninger og tip til administration af Azure AD-forbindelse.

## <a name="monitoring-considerations"></a>Overvågning overvejelser i forbindelse med

Sundhedsovervågning udføres ved hjælp af en række lokale supportmedarbejdere, der er installeret:

- Azure AD-forbindelse installerer en agent, der registrerer oplysninger om synkroniseringen. Brug bladet Azure Active Directory forbinde tilstand i portalen Azure til at overvåge systemtilstand og ydeevnen af Azure AD-forbindelse. Kan finde flere oplysninger under [Brug af Azure AD forbinde sundhed synkroniseres til][aad-health].

- For at overvåge tilstanden for AD DS domæner og mapper fra Azure, skal du installere Azure AD forbinde tilstanden for AD DS agent på en maskine i det lokale domæne. Brug bladet Azure Active Directory forbinde tilstand i portalen Azure til skærm AD DS. Yderligere oplysninger, du [Bruger Azure AD forbinde tilstand med AD DS][aad-health-adds]

- Installer Azure AD forbinde tilstanden for AD FS agent til at overvåge tilstanden for AD FS, der kører på lokalt, og brug bladet Azure Active Directory forbinde tilstand i portalen Azure til at overvåge AD DS. Yderligere oplysninger, du [Bruger Azure AD forbinde tilstand med AD FS][aad-health-adfs]

Du kan finde flere oplysninger om installation af AD forbinde sundhed supportmedarbejdere og deres krav, se [Azure AD Connect sundhed Agent-Installation][aad-agent-installation].

## <a name="sample-solution"></a>Eksempel løsning

Dette afsnit dokumenter trin til oprettelse af en stikprøve løsning, som du kan bruge til at teste konfigurationen af AAD. Løsningen omfatter følgende elementer:

- En n-delt webprogram, efter den struktur, der er beskrevet ved at [Køre FOS for en N-delt arkitektur på Azure][implementing-a-multi-tier-architecture-on-Azure].

- En test klientcomputer. Vi anbefaler, at du bruger en anden VM til denne computer. Konfigurationen af denne VM er ligegyldig, så længe du har administratoradgang og den kan oprette forbindelse til n-delt webprogrammet.

- En simuleret lokalt miljø, der indeholder sit eget domæne, der er oprettet ved hjælp af Active Directory-Domænetjenester.

Scenarier, der viser disse trin, er:

- Aktivere adgang til n-delt webprogrammet kører i skyen til eksterne brugere med AAD give godkendelse af adgangskode.

- Aktivere adgang til n-delt webprogrammet kører i skyen, til brugere, der kører i organisationen, med AAD give godkendelse af adgangskode.

### <a name="prerequisites"></a>Forudsætninger

De efterfølgende trin forudsætter, at følgende forudsætninger:

- Du har et eksisterende Azure-abonnement, kan du oprette ressourcegrupper.

- Du har installeret den [Azure kommandolinjen][azure-cli].

- Du har downloadet og installeret den nyeste version. Se [her] [ azure-powershell-download] vejledning.

- Du har installeret en kopi af [makecert] [ makecert] utility på klientcomputeren test.

- Du har adgang til en udvikling computer, hvor Visual Studio er installeret.

### <a name="create-the-n-tier-web-application-architecture"></a>Oprette n-delt web application arkitektur

1. Oprette en mappe med navnet `Scripts` , der indeholder en undermappe med navnet `Parameters`.

2. Hente [Implementer ReferenceArchitecture.ps1] [ solution-script] PowerShell-script til mappen Scripts.

3. Opret en anden undermappe med navnet Windows i mappen parametre.

4. Hent følgende filer til parametre/Windows-mappe:

    - [virtualNetwork.parameters.json][vnet-parameters-windows]

    - [networkSecurityGroup.parameters.json][nsg-parameters-windows]

    - [webTierParameters.json][webtier-parameters-windows]

    - [businessTierParameters.json][businesstier-parameters-windows]

    - [dataTierParameters.json][datatier-parameters-windows]

    - [managementTierParameters.json][managementtier-parameters-windows]

5. Redigere filen **Implementer ReferenceArchitecture.ps**1 i mappen Scripts og ændre følgende linje for at angive den ressourcegruppe, der skal oprettes eller bruges til at indeholde de VM og ressourcer, der er oprettet af scriptet:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-ntier-rg"
        ```

6. Redigere følgende filer i mappen **parametre/vinduet**s og angive den `resourceGroup` værdi i den `virtualNetworkSettings` sektion i hver af disse filer, der skal være den samme som, du har angivet i filen **Implementer ReferenceArchitecture.ps1** script.

    - networkSecurityGroup.parameters.json

    - webTierParameters.json

    - businessTierParameters.json

    - dataTierParameters.json

    - managementTierParameters.json

7. Åbne en Azure PowerShell-vinduet, gå til mappen Scripts og køre følgende kommando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows ntier
        ```

    Erstatte **<subscription id>** med dit Azure abonnement-ID.

    For **<location>**, Angiv en Azure region, som *eastus* eller *westus*.

8. Når scriptet er færdig, skal du bruge Azure-portalen til at hente den offentlige IP-adressen på web-niveau justering af belastning (*ra-aad-ntier-web-kg*):

    [! [18]][18]

9. Log på test klienten på computeren (eller VM), og Bekræft, at du kan få adgang til web-niveauet ved hjælp af Internet Explorer til at gå til den offentlige IP-adressen på web-niveau justering af belastning. IIS standardsiden skal vises.

### <a name="simulate-configuration-of-a-public-web-site"></a>Simulere konfiguration af et offentligt websted

>[AZURE.NOTE] Følgende trin simulere at knytte web-niveauet til DNS-navn www.contoso.com ved at ændre filen hosts på klientcomputeren test. Desuden er web lag FOS konfigureret med selvsignerede certifikater og FALSK vært nøglecenter. Dette er til test formål kun og **du ikke skal bruge disse teknikker i et produktionsmiljø**.

1. Redigere filen **C:\Windows\System32\drivers\etc\hosts** ved hjælp af Notesblok på klientcomputeren test, og Tilføj en post, der knytter navn www.contoso.com til den offentlige IP-adressen på web-niveau justering af belastning:

        ```text
        # Copyright (c) 1993-2009 Microsoft Corp.
        #
        # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
        #
        # This file contains the mappings of IP addresses to host names. Each
        # entry should be kept on an individual line. The IP address should
        # be placed in the first column followed by the corresponding host name.
        # The IP address and the host name should be separated by at least one
        # space.
        #
        # Additionally, comments (such as these) may be inserted on individual
        # lines or following the machine name denoted by a '#' symbol.
        #
        # For example:
        #
        #      102.54.94.97     rhino.acme.com          # source server
        #       38.25.63.10     x.acme.com              # x client host
        
        # localhost name resolution is handled within DNS itself.
        #   127.0.0.1       localhost
        #   ::1             localhost
        
        52.165.38.64    www.contoso.com
        ```

2. Kontrollér, at du kan nu bladre www.contoso.com fra klientcomputeren test. Samme IIS standardsiden skal vises som før.

3. Åbn en kommandoprompt som Administrator på klientcomputeren test og bruge værktøjet makecert til c
4. Ret certifikat fra et falske rod nøglecenter:

        ```
        makecert -sky exchange -pe -a sha256 -n "CN=MyFakeRootCertificateAuthority" -r -sv MyFakeRootCertificateAuthority.pvk MyFakeRootCertificateAuthority.cer -len 2048
        ```

    Kontrollere, at der oprettes følgende filer:

    - MyFakeRootCertificateAuthority.cer

    - MyFakeRootCertificateAuthority.pvk

4. Kør følgende kommando for at installere test rodnøglecenter:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Bruge nøglecenter test til at generere et certifikat til www.contoso.com:

        ```
        makecert -sk pkey -iv MyFakeRootCertificateAuthority.pvk -a sha256 -n "CN=www.contoso.com" -ic MyFakeRootCertificateAuthority.cer -sr localmachine -ss my -sky exchange -pe
        ```

6. Kør kommandoen **mmc** , og Tilføj snap-in-programmet Certifikater til computerkonto for den lokale computer.

7. I den */Certificates (lokalcomputer) / personlig/certifikat/* gemme, eksportere www.contoso.com certifikat med den tilhørende privat nøgle til en fil med navnet www.contoso.com.pfx:

    [! [20]][20]

8. Gå tilbage til portalen Azure og oprette forbindelse til administration af lag VM (*ra-aad-ntier-mgmt-vm1*). Standardnavnet er *testuser* med adgangskode *AweS0me@PW*:

    [! [21]][21]
    
9. Administration af lag VM, til at oprette forbindelse til hver af web-niveauet FOS igen med brugernavn *testuser* og din adgangskode *AweS0me@PW*, og udføre følgende opgaver. Bemærk, at FOS har private adresser IP 10.0.1.4, 10.0.1.5 og 10.0.1.6:

    >[AZURE.NOTE] Web niveau FOS kun have private IP-adresser. Du kan kun oprette forbindelse til dem ved hjælp af administration af lag VM.

    1. Kopiere filer *www.contoso.com.pfx* og *MyFakeRootCertificateAuthority.cer* fra klientcomputeren test.
    
    2. Åbn en kommandoprompt som Administrator, flytte til den mappe, der holder de filer, som du kopierede, og Kør følgende kommandoer:
    
        ```
        certutil.exe -privatekey -importPFX my www.contoso.com.pfx NoExport

        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

    3. Køre den `mmc` kommandoen, tilføje snap-in-programmet Certifikater til computerkonto for den lokale computer og bekræfte, at følgende certifikater er installeret:

        - \Certificates (lokal Computer) \Personal\Certificates\ www.contoso.com

        - \Certificates (lokal Computer) \Trusted rod certificering Authorities\Certificates\MyFakeRootCertificateAuthority

    4. Start Internet Information Services (IIS) Manager-konsollen, og gå til *Sites\Default websted* på computeren.

    5. Klik på *bindinger*, og Tilføj en https-binding ved hjælp af www.contoso.com SSL-certifikat i ruden *Handlinger* :

        [! [22]][22]

10. Gå tilbage til klientcomputeren test, og Bekræft, at du kan nu gå til https://www.contoso.com.

### <a name="create-an-azure-active-directory-tenant"></a>Oprette en Azure Active Directory-lejer

1. Ved hjælp af portalen Azure, oprette en Azure Active Directory-lejer som *myaadname*. onmicrosoft.com, hvor *myaadname* er et mappenavn, som du har valgt.

2. Tilføje en bruger med navnet *administrator* med rollen GlobalAdmin til mappen. Noter om den nyligt oprettede adgangskode.

3. Ved hjælp af Internet Explorer, gå til https://account.activedirectory.windowsazure.com/ og logge på som admin@ *myaadname*. onmicrosoft.com. Ændre din adgangskode, når du bliver bedt om det.

### <a name="create-and-deploy-a-test-web-application"></a>Oprette og installere en test webprogram

1. Ved hjælp af Visual Studio på udviklingscomputeren,, oprette en ASP.NET-webprogram med navnet ContosoWebApp1 (brug .NET Framework 4.5.2):

    [! [23]][23]

2. Vælg skabelonen, *MVC* , ændre godkendelsen til *arbejde og skole-konti*, og Angiv navnet på din AAD lejer. Ikke oprette en App-tjeneste i skyen.

    [! [24]][24]

3. Oprette og køre programmet for at teste godkendelsen. Indtast et kontonavn på siden Log på admin@ *myaadname*. onmicrosoft.com, angive adgangskoden, og klik derefter på *Log på*:

    [! [25]][25]

4. Bekræft, at AAD beder om tilladelse til at logge på og læse din profil, og derefter begynder at køre webprogrammet identitet administrator.

5. Luk Internet Explorer og vende tilbage til Visual Studio.

6. Åbn filen web.config og i den `<appSettings>` kan du ændre værdien af nøglen *ida: PostLogoutRedirectUri* til *https://www.contoso.com:443 /*. Gem filen.

7. Højreklik på ContosoWebApp1 projektet i *Solution Explorer* -vinduet, klik på *Publicer*.

8. Klik på *brugerdefineret*i vinduet *Udgiv websted* . Oprette en brugerdefineret profil med navnet *ContosoWebApp1*.

9. Angive *Publicer metode* til *Filsystem* på siden *forbindelsen* og angive en mappe med navnet *ContosoWebApp*, findes i et praktisk sted på computeren udvikling.

10. På siden *Indstillinger* , du Angiv *konfiguration* til *version*.

11. Klik på *Publicer*på siden *Preview* .

12. Oprette forbindelse til hver webserver tur. (via administration af lag VM) og udføre følgende opgaver:

    1. Kopiere mappen *ContosoWebApp* og dens indhold fra udviklingscomputeren til mappen *C:\inetpub* .

    2. Ved hjælp af Internet Information Services (IIS) Manager-konsollen, gå til *Sites\Default websted* på computeren.

    3. Klik på *Indstillinger for datotype*i ruden *Handlinger* , og ændre den fysiske sti til webstedet til *%SystemDrive%\inetpub\ContosoWebApp*:

        [! [26]][26]

### <a name="publish-the-test-web-application-through-aad"></a>Publicere webprogrammet test gennem AAD

1. Log på portalen Azure, og gå til mappen AAD.

2. Klik på ContosoWebApp1 program under *programmer* .

3. Kontroller, at dit program er føjet til mappen, og klik derefter på fanen *KONFIGURER* .

4. Ændre *SIGN-ON URL-adressen* til https://www.contoso.com:443, og angiv *Svar URL-adressen* til https://www.contoso.com:443 (den samme URL-adresse).

5. Gemme konfigurationen.

6. Gå til https://www.contoso.com på klientcomputeren test. Bekræft, at AAD beder dig om dine legitimationsoplysninger, og derefter logge på.

>[AZURE.NOTE] Dette er slutpunktet for det første scenarie: aktivere adgang til n-delt webprogrammet kører i skyen til eksterne brugere med AAD give godkendelse af adgangskode.

### <a name="create-a-simulated-on-premises-environment-with-active-directory"></a>Oprette en simuleret lokalt miljø med Active Directory

Det lokale miljø indeholder to domænecontrollere for den `contoso.com` domæne og to servere som vært for Azure AD-forbindelse synkronisere tjeneste. Servere som vært for Azure AD-forbindelse er ikke medlem af et domæne.

1. Åbn Stifinder, gå tilbage til mappen Scripts, der indeholder det script, der bruges til at oprette webprogrammet N lag.

2. Tilføje en anden sub-mappe med navnet i mappen parametre `Onpremise`.

3. Hent følgende filer til parametre/Premise mappe:

    - [Tilføj-tilføjer-domæne-controller.parameters.json][add-adds-domain-controller-parameters]

    - [oprette-tilføjer-skov-extension.parameters.json][create-adds-forest-extension-parameters]

    - [virtualMachines adc.parameters.json][virtualMachines-adc-parameters]

    - [virtualMachines-adc-joindomain.parameters.json][virtualMachines-adc-joindomain-parameters]

    - [virtualMachines adds.parameters.json][virtualMachines-adds-parameters]

    - [virtualNetwork.parameters.json][virtualNetwork-parameters]

    - [virtualNetwork tilføjer dns.parameters.json][virtualNetwork-adds-dns-parameters]

5. Redigere filen Implementer ReferenceArchitecture.ps1 i mappen Scripts og ændre følgende linje for at angive den ressourcegruppe, der skal oprettes eller bruges til at indeholde de VM og ressourcer, der er oprettet af scriptet:

        ```powershell
        # PowerShell
        $resourceGroupName = "ra-aad-onpremise-rg"
        ```

6. Redigere følgende filer i mappen parametre/Premise og angive den `resourceGroup` værdi i den `virtualNetworkSettings` sektion i hver af disse filer, der skal være den samme som, du har angivet i filen Implementer ReferenceArchitecture.ps1 script.

    - virtualMachines adds.parameters.json

    - virtualMachines adc.parameters.json

    - virtualNetwork.parameters.json

    - virtualNetwork tilføjer dns.parameters.json

7. Åbne en Azure PowerShell-vinduet, gå til mappen Scripts og køre følgende kommando:

        ```powershell
        .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows onpremise
        ```

    Erstatte `<subscription id>` med dit Azure abonnement-ID.

    For `<location>`, Angiv en Azure region, såsom `eastus` eller `westus`.

### <a name="install-and-configure-the-azure-ad-connect-sync-service"></a>Installere og konfigurere tjenesten Azure AD-forbindelse synkronisering

Konfigurationen illustreret i disse trin består af to forekomster af tjenesten Azure AD-forbindelse synkronisering. Først er aktiv, mens andet er konfigureret i midlertidige tilstand for at give hurtig failover og høj tilgængelighed, hvis den første server mislykkes.

1. Ved hjælp af portalen Azure, gå til ressourcegruppen holder FOS for synkroniseringstjenester Azure AD-forbindelse (*ra-aad-Premise-indbyggede rg* som standard), og oprette forbindelse til *ra-aad-Premise-adc-vm1* VM. Log på som *testuser* med adgangskode *AweS0me@PW*.

2. Hente Azure AD-forbindelse fra [her][aad-connect-download].

3. Kør installationsprogrammet til Azure AD-forbindelse og udføre en installation, ved hjælp af indstillingen *Tilpas* .

    >[AZURE.NOTE] Du kan ikke bruge *Hurtige* indstillinger, fordi den VM værtstjeneste Azure AD-forbindelse er synkronisering ikke er medlem af et domæne.

4. På siden *installere påkrævede komponenter* Undlad at indstillingerne for valgfri konfiguration til at acceptere standardindstillingerne.

5. Vælg *Synkronisering af adgangskoder*, på siden *bruger - logon* .

6. På siden *Opret forbindelse til Azure AD* angive admin@ *myaadname*. onmicrosoft.com, hvor *myaadname* er navnet på din AAD lejer. Skriv adgangskoden til kontoen Administrator.

7. Angiv contoso.com til området på siden *forbinde dine adresselister* (skriver værdien i, fordi den ikke vises på listen ned), Angiv CONTOSO\testuser for brugernavnet, skal du angive AweS0me@PW om adgangskoden, og klik derefter på *Tilføj mappe*.

8. På siden *Azure AD - logon konfiguration* skal du acceptere standardværdien for brugerens hovednavn. Markér *Fortsæt uden en hvilken som helst bekræftede domæner*.

    >[AZURE.NOTE] Mappen contoso.com er angivet som *Ikke godkendt*. For at bekræfte et domænenavn, skal du oprette en TXT-post for dit domænenavn registrator. I dette eksempel er det lokale domæne ikke registreret eksternt. Yderligere oplysninger finder du se [tilføje et brugerdefineret domænenavn til Azure Active Directory][aad-custom-directory].

9. Vælg *Synkroniser alle domæner og afdelinger* (standard) på siden *Domain og OU filtrering* .

10. På siden *entydigt identificerer dine brugere* skal du acceptere standardværdierne.

11. Vælg *Synkroniser alle brugere og enheder* (standard) på siden *filtrere brugere og enheder* .

12. Vælg *adgangskode der ikke er gemt*på siden *valgfrie funktioner* .

13. Vælge *starte synkroniseringen når konfigurationen er fuldført*på siden *klar til at konfigurere* , men lade *Aktivér midlertidige tilstanden* ikke er markeret.

14. Bekræft, at konfigurationen er fuldført uden fejl, og Afslut installationsprogrammet.

15. Oprette forbindelse til *ra-aad-Premise-adc-vm2* VM fra portalen Azure. Log på som *testuser* med adgangskode *AweS0me@PW*.

16. Hente Azure AD-forbindelse, og derefter køre installationsprogrammet.

17. Gennemgå guiden, og besvare som beskrevet i trin 3 til 12.

18. Vælg *Start processen til synkronisering, når konfigurationen er fuldført*, og **også vælge** *Aktivér midlertidige tilstanden*på siden *klar til at konfigurere* . Derved tjenesten Azure AD-forbindelse sync at hente oplysninger om konti og objekter fra contoso.com domænet og gemme dem i databasen, men det sende ikke disse oplysninger til din AAD lejer.

14. Bekræft, at konfigurationen er fuldført uden fejl, og Afslut installationsprogrammet.

### <a name="test-the-aad-configuration"></a>Test konfigurationen AAD

1. Ved hjælp af portalen Azure, skifte til adresselisten AAD, åbne bladet Azure Active Directory, klik på *brugere og grupper*og klik derefter på *alle brugere* for at få vist listen over brugere og grupper, der er synkroniseret med kataloget. Du burde se brugere til følgende konti:

    - administrator (admin@ *myaadname*. onmicrosoft.com)

    - Lokal synkronisering adresselistekonto (Sync_ADC1_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

    - Lokal synkronisering adresselistekonto (Sync_ADC2_*nnnnnnnnnnnn*@*myaadname*. onmicrosoft.com)

2. Gå til ressourcegruppen holder FOS ved AD DS domæne enheder (*ra-aad-Premise-indbyggede rg* som standard) i Azure-portalen, og oprette forbindelse til *ra-aad-Premise-ad-vm1* VM. Log på som *testuser* med adgangskode *AweS0me@PW*.

3. Ved hjælp af konsollen *Active Directory-brugere og computere* , tilføje en ny domænebruger med navnet Diane Tibbot med logonnavn dianet@contoso.com. Angive en adgangskode efter eget valg. Gøre et medlem af den lokale administratorgruppe til brugeren (dette er kun, så du kan logge lokalt på som denne bruger senere - kun maskiner i domænet er DCs).

4. Skifte til *ra-aad-Premise-adc-vm1* VM, Åbn en PowerShell-vinduet, og Kør følgende kommandoer:

        ```[powershell]
        Import-Module ADSync
        Start-ADSyncSyncCycle -PolicyType Delta
        ```

    Kontrollér, at kommandoen returnerer *succes*.

    >[AZURE.NOTE] Dette trin er nødvendigt, fordi som standard synkroniseringen er planlagt til at køre med 30 minutter intervaller. Disse kommandoer tvinge en synkronisering skal udføres med det samme.

5. Gå tilbage til portalen Azure, Skift til adresselisten AAD, Åbn bladet Azure Active Directory, klik på *brugere og grupper*, og klik derefter på *alle brugere*. Du bør nu se Diane Tibbot (dianet@ *myaadname*. onmicrosoft.com) vises i listen over brugere.

6. Klik på *Virksomhedens programmer*i bladet Azure Active Directory, og klik derefter på *alle programmer*. Klik på *ContosoWebApp1* programmet, og klik derefter på *brugere og grupper*. Klik på *Tilføj*på værktøjslinjen. Klik på *brugere og grupper*, og vælg *Elisabeth Tibbot*. Klik på *Tildel*.

7. Ved hjælp af Internet Explorer, gå til webstedet https://account.activedirectory.windowsazure.com. Log på som dianet@ *myaadname*. onmicrosoft.com med den adgangskode, du tidligere har angivet.

    >[AZURE.NOTE] Skal ikke klikke på ikonet ContosoWebApp på listen over programmer. AAD forsøger at finde webprogrammet på den offentligt DNS-adresse til www.contoso.com, som er forskellig fra adressen på dit web lag justering af belastning.

8. Klik på fanen *profil* . Oplysninger om brugeren (Hvis du har angivet en når det blev oprettet) skal vises.

    >[AZURE.NOTE] Hvis du klikker på *Skift adgangskode*, kan du ikke udføre denne opgave, som denne handling skal aktiveres af en administrator først. Du kan finde flere oplysninger i [Introduktion til administration af adgangskoder][aad-password-management].

### <a name="enable-on-premises-users-to-run-the-application-by-using-authentication-through-aad"></a>Aktivere lokale brugere køre programmet ved hjælp af godkendelse gennem AAD

1. Gå tilbage til *ra-aad-Premise-ad-vm1* domænenavn fra domænecontrolleren VM.

2. Åbn *DNS Manager* -konsollen, og Tilføj en ny post host for www.contoso.com. Angiv den offentlige IP-adressen på web-niveau justering af belastning.

3. Kopiér filen *MyFakeRootCertificateAuthority.cer* fra klientcomputeren test (du har oprettet denne filer i proceduren [Simulate konfiguration af et offentligt websted](#simulate-configuration-of-a-public-web-site)
    
4. Åbn en kommandoprompt som Administrator, flytte til den mappe, hvor du holder den fil, du lige har kopieret og køre følgende kommando:

        ```
        certutil.exe -addstore Root MyFakeRootCertificateAuthority.cer
        ```

5. Ved hjælp af Internet Explorer, gå til https://www.contoso.com. Kontrollér, at siden AAD logon for webprogrammet ContosoWebApp1 vises.

6. Log på som dianet@ *myaadname*. onmicrosoft.com. Programmet skal køre og logge dig på korrekt.

## <a name="next-steps"></a>Næste trin

- Lær de bedste fremgangsmåder til at [udvide dit lokale tilføjer domæne til Azure][adds-extend-domain]

- Lær de bedste fremgangsmåder til [oprettelse af et område, der tilføjer ressource] [ adds-resource-forest] i Azure

<!-- links -->
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[script]: #sample-solution-script
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[active-directory-domain-services]: https://technet.microsoft.com/library/dd448614.aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[azure-active-directory]: ../active-directory-domain-services/active-directory-ds-overview.md
[azure-ad-connect]: ../active-directory/active-directory-aadconnect.md
[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[aad-explained]: https://youtu.be/tj_0d4tR6aM
[aad-editions]: ../active-directory/active-directory-editions.md
[guidance-adds]: ./guidance-iaas-ra-secure-vnet-ad.md
[sla-aad]: https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/
[azure-multifactor-authentication]: ../multi-factor-authentication/multi-factor-authentication.md
[aad-conditional-access]: ../active-directory//active-directory-conditional-access.md
[aad-dynamic-membership-rules]: ../active-directory/active-directory-accessmanagement-groups-with-advanced-rules.md
[aad-dynamic-memberships]: https://youtu.be/Tdiz2JqCl9Q
[aad-user-sign-in]: ../active-directory/active-directory-aadconnect-user-signin.md
[aad-sync-requirements]: ../active-directory/active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md
[aad-topologies]: ../active-directory/active-directory-aadconnect-topologies.md
[aad-filtering]: ../active-directory/active-directory-aadconnectsync-configure-filtering.md
[aad-scalability]: https://blogs.technet.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/
[aad-connect-sync-default-rules]: ../active-directory/active-directory-aadconnectsync-understanding-default-configuration.md
[aad-identity-protection]: ../active-directory/active-directory-identityprotection.md
[aad-password-management]: ../active-directory/active-directory-passwords-customize.md
[aad-application-proxy]: ../active-directory/active-directory-application-proxy-enable.md
[aad-connect-sync-operational-tasks]: ../active-directory/active-directory-aadconnectsync-operations.md#staging-mode
[aad-custom-domain]: ../active-directory/active-directory-add-domain.md
[aad-powershell]: https://msdn.microsoft.com/library/azure/mt757189.aspx
[aad-sync-disaster-recovery]: ../active-directory/active-directory-aadconnectsync-operations.md#disaster-recovery
[aad-sync-best-practices]: ../active-directory/active-directory-aadconnectsync-best-practices-changing-default-configuration.md
[aad-adfs]: ../active-directory/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs
[aad-health]: ../active-directory/active-directory-aadconnect-health-sync.md
[aad-health-adds]: ../active-directory/active-directory-aadconnect-health-adds.md
[aad-health-adfs]: ../active-directory/active-directory-aadconnect-health-adfs.md
[aad-agent-installation]: ../active-directory/active-directory-aadconnect-health-agent-install.md
[aad-reporting-guide]: ../active-directory/active-directory-reporting-guide.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-powershell-download]: ../powershell-install-configure.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/Deploy-ReferenceArchitecture.ps1
[vnet-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/webTier.parameters.json
[businesstier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/businessTier.parameters.json
[datatier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/dataTier.parameters.json
[managementtier-parameters-windows]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/windows/managementTier.parameters.json
[makecert]: https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/add-adds-domain-controller.parameters.json
[create-adds-forest-extension-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/create-adds-forest-extension.parameters.json
[virtualMachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adds.parameters.json
[virtualNetwork-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork.parameters.json
[virtualNetwork-adds-dns-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualNetwork-adds-dns.parameters.json
[virtualMachines-adc-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc.parameters.json
[virtualMachines-adc-joindomain-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-aad/parameters/onpremise/virtualMachines-adc-joindomain.parameters.json
[aad-connect-download]: http://www.microsoft.com/download/details.aspx?id=47594
[aad-custom-directory]: ../active-directory/active-directory-add-domain.md
[aad-password-management]: ../active-directory/active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords
[adds-extend-domain]: ./guidance-identity-adds-extend-domain.md
[adds-resource-forest]: ./guidance-identity-adds-resource-forest.md
[0]: ./media/guidance-identity-aad/figure1.png "Skyen identitet arkitektur ved hjælp af Azure Active Directory"
[1]: ./media/guidance-identity-aad/figure2.png "Enkelt skov, enkelt AAD directory topologi"
[2]: ./media/guidance-identity-aad/figure3.png "Flere områder, enkelt AAD directory topologi"
[4]: ./media/guidance-identity-aad/figure5.png "Arrangere servertopologi"
[5]: ./media/guidance-identity-aad/figure6.png "Flere AAD kataloger topologi"
[6]: ./media/guidance-identity-aad/figure7.png "Hvis du vælger en brugerdefineret installation af Azure Active Directory forbinde-synkronisering med en bestemt forekomst af SQL Server"
[7]: ./media/guidance-identity-aad/figure8.png "Angive metoden SSO for bruger-logon"
[8]: ./media/guidance-identity-aad/figure9.png "Angive domæne og OU filtreringsindstillinger"
[9]: ./media/guidance-identity-aad/figure10.png "Aktivere adgangskode tilbageskrivning"
[10]: ./media/guidance-identity-aad/figure11.png "Azure AD management blade i portalen"
[11]: ./media/guidance-identity-aad/figure12.png "Konsollen Azure AD-forbindelse"
[12]: ./media/guidance-identity-aad/figure13.png "Fanen Handlinger i synkronisering Service Manager"
[13]: ./media/guidance-identity-aad/figure14.png "Fanen forbindelser i synkronisering Service Manager"
[14]: ./media/guidance-identity-aad/figure15.png "Redigeringsprogrammet til synkronisering af regler"
[15]: ./media/guidance-identity-aad/figure16.png "Bladet Azure Active Directory forbinde tilstand i Azure-portalen med synkronisering tilstand"
[16]: ./media/guidance-identity-aad/figure17.png "Bladet Azure Active Directory forbinde tilstand i portalen Azure viser AD DS tilstand"
[17]: ./media/guidance-identity-aad/figure18.png "Sikkerhedsrapporter, der er tilgængelige i portalen Azure"
[18]: ./media/guidance-identity-aad/figure19.png "Portalen Azure fremhævning af den offentlige IP-adressen på web-niveau justering af belastning"
[19]: ./media/guidance-identity-aad/figure20.png "Brug af Internet Explorer til at gå til den offentlige IP-adressen på web-niveau justering af belastning"
[20]: ./media/guidance-identity-aad/figure21.png "Snap-in'en Certifikater viser www.contoso.com certifikatet"
[21]: ./media/guidance-identity-aad/figure22.png "Oprette forbindelse til administration af lag VM"
[22]: ./media/guidance-identity-aad/figure23.png "Oprette HTTPS-binding for standardwebstedet"
[23]: ./media/guidance-identity-aad/figure24.png "Oprette webprogrammet ContosoWebApp1"
[24]: ./media/guidance-identity-aad/figure25.png "Indstille egenskaber for godkendelse af webprogrammet ContosoWebApp1"
[25]: ./media/guidance-identity-aad/figure26.png "Logge på Azure AAD fra ContosoWebApp1-webprogrammet"
[26]: ./media/guidance-identity-aad/figure27.png "Ændre mappen til standard-webstedet"