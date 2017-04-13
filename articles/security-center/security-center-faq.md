<properties
   pageTitle="Azure Sikkerhedscenter ofte stillede spørgsmål (FAQ) | Microsoft Azure"
   description="Disse ofte stillede spørgsmål finder du svar på spørgsmål om Azure Sikkerhedscenter."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure Sikkerhedscenter stillede ofte spørgsmål (FAQ)

Disse ofte stillede spørgsmål finder du svar på spørgsmål om Azure Sikkerhedscenter, en tjeneste, der hjælper dig med at forhindre, registrerer og besvare trusler med øget indsigt i og kontrol over sikkerheden for dine Microsoft Azure-ressourcer.

## <a name="general-questions"></a>Generelle spørgsmål

### <a name="what-is-azure-security-center"></a>Hvad er Azure Security Center?
Azure Sikkerhedscenter hjælper dig med at forhindre, registrere og besvare trusler med øget indsigt i og kontrol over sikkerheden for dine Azure ressourcer. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

### <a name="how-do-i-get-azure-security-center"></a>Hvordan får jeg Azure Security Center?
Azure Sikkerhedscenter er aktiveret til dit Microsoft Azure-abonnement og adgang til fra [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). ([Log på portalen](https://portal.azure.com), Vælg **Gennemse**, og Rul ned til **Sikkerhedscenter**).  

## <a name="billing"></a>Fakturering

### <a name="how-does-billing-work-for-azure-security-center"></a>Hvordan fungerer fakturering sammen til Azure Security Center?
Sikkerhedscenter fås i to niveauer: gratis og standardversionen.

Det gratis niveau gør det muligt at angive sikkerhedspolitikker og modtage sikkerhedsadvarsler, hændelser og anbefalinger, der fører dig gennem processen med at konfigurere nødvendige kontrolelementer. Du kan også overvåge tilstanden sikkerhed dine Azure ressourcer og partnerløsninger integreret med abonnementet Azure med det gratis niveau.

Det Standard niveau indeholder gratis niveau plus avancerede funktioner registreringer: trussel intelligence, funktionsmæssige analyse, nedbrud analyse og anomali registrering. En gratis 90-dages prøveversion af Standard-niveauet er tilgængelig. For at opgradere skal du vælge priser niveau i [sikkerhedspolitik](security-center-policies.md#setting-security-policies-for-subscriptions). Se [Sikkerhedscenter priser](security-center-pricing.md) for at lære mere.

## <a name="data-collection"></a>Dataindsamling

Sikkerhedscenter indsamler data fra din virtuelle maskiner for at vurdere deres sikkerhedstilstand, giver anbefalinger til sikkerhed, og giver dig besked om trusler. Når du først åbne Sikkerhedscenter, aktiveres dataindsamling på alle virtuelle maskiner i dit abonnement. Dataindsamling anbefales, men du kan framelding ved at [deaktivere dataindsamling](#how-do-i-disable-data-collection) i politikken Sikkerhedscenter.

### <a name="how-do-i-disable-data-collection"></a>Hvordan deaktiverer jeg dataindsamling?

Du kan deaktivere **dataindsamling** til et abonnement i sikkerhedspolitikken, når som helst. ([Log på portalen Azure](https://portal.azure.com), Vælg **Gennemse**, Vælg **Sikkerhedscenter**, og vælg **politik**.)  Når du vælger et abonnement, åbnes en ny blade, og giver dig mulighed for at deaktivere **dataindsamling** . Vælg indstillingen **Slet supportmedarbejdere** i det øverste båndet for at fjerne supportmedarbejdere fra eksisterende virtuelle computere.

> [AZURE.NOTE] Sikkerhedspolitikker kan angives i Azure abonnementsniveau og ressource grupperingsniveau, men du skal vælge et abonnement på deaktivere dataindsamling.

### <a name="how-do-i-enable-data-collection"></a>Hvordan aktiverer jeg dataindsamling?
Du kan aktivere dataindsamling for dine Azure abonnementer i sikkerhedspolitikken. Vælg **Gennemse**for at aktivere dataindsamling, [Log på portalen Azure](https://portal.azure.com), Vælg **Sikkerhedscenter**, og vælg **politik**. Angive **dataindsamling** til **på** og konfigurere kontiene lagerplads, hvor du vil data indsamles til (se spørgsmål "[hvor er mine data gemt?](#where-is-my-data-stored)"). Når **dataindsamling** er aktiveret, indsamler oplysninger om sikkerhed konfiguration og begivenhed automatisk fra alle understøttede virtuelle maskiner i abonnement.

> [AZURE.NOTE] Sikkerhedspolitikker kan angives i Azure abonnementsniveau og ressource grupperingsniveau, men konfigurationen af dataindsamling optræder i niveauet abonnement.

### <a name="what-happens-when-data-collection-is-enabled"></a>Hvad sker der, når dataindsamling er aktiveret?
Dataindsamling er aktiveret via Azure overvågning Agent og filtypenavnet Azure sikkerhed overvågning. Filtypenavnet Azure sikkerhed overvågning scanner for forskellige sikkerhed relevante konfiguration og sender den til [Begivenhed sporing til Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) spor. Desuden opretter operativsystemet hændelseslogposter.  Azure overvågning Agent læser hændelseslogposter og ETW sporer og kopierer dem til kontoen lagerplads til analyse.  Dette er lagerplads-konto, du har konfigureret i sikkerhedspolitikken. Du kan finde flere oplysninger om kontoen lagerplads, se spørgsmål "[hvor er mine data gemt?](#where-is-my-data-stored)"

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>Påvirker filtypenavnet overvågning Agent eller sikkerhed overvåge ydeevnen for mine Server(e)?
Agent og filtypenavn bruger en nominel mængde systemressourcer og skal have lille betydning for ydeevnen.

### <a name="where-is-my-data-stored"></a>Hvor er mine data gemt?
For hvert område, hvor du har virtuelle maskiner, der kører, kan du vælge kontoen lagerplads, hvor data, der indsamles fra disse virtuelle maskiner er gemt. Det gør det nemt for dig at bevare data i det samme geografiske område til beskyttelse af personlige oplysninger og data højhedsområde formål. Du kan vælge kontoen lagerplads til et abonnement, i sikkerhedspolitikken. ([Log på portalen Azure](https://portal.azure.com), Vælg **Gennemse**, Vælg **Sikkerhedscenter**, og vælg **politik**.) Når du klikker på et abonnement, åbnes en ny blade. Vælg **Vælg lagerplads konti** for at vælge et område.

> [AZURE.NOTE] Sikkerhedspolitikker kan angives i Azure abonnementsniveau og ressource grupperingsniveau, men at vælge et område til kontoen lagerplads optræder i niveauet abonnement.

Hvis du vil vide mere om Azure lager og lagerplads konti, skal du se [Lagerplads dokumentation](https://azure.microsoft.com/documentation/services/storage/) og [om Azure lagerplads konti](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>Bruge Azure Sikkerhedscenter

### <a name="what-is-a-security-policy"></a>Hvad er en sikkerhedspolitik?
En sikkerhedspolitik definerer de kontrolelementer, der er anbefalet til ressourcer inden for den angivne abonnement eller ressourcegruppe. I Azure Sikkerhedscenter definerer du politikker for dine Azure abonnementer og ressourcegrupper afhængigt af din virksomheds sikkerhedskrav og typen af programmer eller følsomheden for dataene i hvert abonnement.

Ressourcer, der bruges til udvikling eller test kan for eksempel har forskellige sikkerhedskrav end dem, der anvendes til fremstilling programmer. På samme måde kan programmer med organiseret data som PII (personlige oplysninger) kræver et højere niveau af sikkerhed. De er aktiveret i Azure Sikkerhedscenter sikkerhedspolitikker vil drive anbefalinger til sikkerhed og overvågning. Få mere at vide om sikkerhedspolitikker under [sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md).

> [AZURE.NOTE] I tilfælde af en konflikt mellem abonnement niveau politik og ressource-niveau Gruppepolitik prioriteres ressource Gruppepolitik for niveau.

### <a name="who-can-modify-a-security-policy"></a>Hvem der kan redigere en sikkerhedspolitik?
Sikkerhedspolitikker er konfigureret for hver abonnement eller ressourcegruppe. Hvis du vil ændre en sikkerhedspolitik abonnementsniveau eller ressource grupperingsniveau, skal du være en ejer eller bidragyder af dette abonnement.

For at lære at konfigurere en sikkerhedspolitik, skal du se [indstillingen sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Hvad er en sikkerhed anbefaling?
Azure Sikkerhedscenter analyserer tilstanden sikkerhed for ressourcerne Azure. Når potentielle sikkerhedsrisici identificeres, oprettes anbefalinger. Anbefalinger fører dig gennem processen med at konfigurere det nødvendige kontrolelement. Eksempler kan nævnes:

- Klargøring af Antimalwareprogram til at identificere og fjerne skadelig software
- Konfiguration af [Netværk sikkerhedsgrupper](../virtual-network/virtual-networks-nsg.md) og regler til kontrolelementet trafik til den virtuelle maskiner
- Klargøring af en firewall til webprogrammer kan beskytte mod angreb målretning af dine webprogrammer
- Implementering manglende systemopdateringer
- Vælge en adresse til OS konfigurationer, der ikke stemmer overens med de anbefalede grundlinjer

Kun anbefalinger, der er aktiveret i sikkerhedspolitikker er vist her.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hvordan kan jeg se mine Azure ressourcer aktuelle sikkerhedstilstand?
Et **ressourcer sundhed** felt på bladet **Sikkerhedscenter** viser den overordnede sikkerhed stilling i dit miljø, opdelt efter virtuelle maskiner, webprogrammer og andre ressourcer. Hver ressource har en indikator viser, hvis en hvilken som helst potentielle sikkerhedsrisici har identificeret. Klik på feltet ressourcer sundhed viser dine ressourcer og identificerer hvor handling er påkrævet eller problemer kan findes.

### <a name="what-triggers-a-security-alert"></a>Hvad udløser en sikkerhedsadvarsel?
Azure Sikkerhedscenter automatisk indsamler, analyserer og fuses logdata fra Azure ressourcerne, netværk og partnerløsninger som Antimalwareprogram og firewalls. Når trusler registreres, oprettes der en sikkerhedsadvarsel. Som eksempler kan nævnes påvisning af:

- Kompromitteret virtuelle maskiner kommunikere med kendte skadelig IP-adresser
- Avancerede malware, der er fundet ved hjælp af Windows-fejlrapportering
- Råstyrke angreb på virtuelle maskiner
- Sikkerhedsadvarsler fra integreret sikkerhed partnerløsninger som Antimalware- eller Web Application Firewalls

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Hvad er forskellen mellem trusler fundet og besked på via Microsoft Security Response Center kontra Azure Security Center?
Microsoft sikkerhed svar Center (MSRC) udfører Vælg sikkerhed overvågning af Azure Netværks- og infrastruktur og modtager truslen intelligence og misbrug klager fra tredjeparter. Når MSRC bliver opmærksom på, at kundedata har fået adgang til en ulovlig eller uautoriserede part eller, kundens brug af Azure ikke opfylder betingelserne for acceptabel brug, om en sikkerhed hændelsen manager kunden. Meddelelse om opstår typisk ved at sende en mail til de sikkerhed kontaktpersoner, der er angivet i Azure Sikkerhedscenter eller ejeren af Azure-abonnement, hvis der ikke er angivet en sikkerhed kontakt.

Sikkerhedscenter er en Azure-tjeneste, der løbende overvåger kundens Azure miljø og anvender analytics at automatisk registrere et bredt udvalg af potentielt skadelig aktivitet. Disse registreringer fået som sikkerhedsadvarsler i dashboardet for Sikkerhedscenter.

### <a name="how-are-permissions-handled-in-azure-security-center"></a>Hvordan håndteres tilladelser i Azure Security Center?
Azure Sikkerhedscenter understøtter rollebaseret adgang. Hvis du vil vide mere om rollebaseret adgangskontrol (RBAC) i Azure, skal du se [Azure Active Directory rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md).

Når en bruger åbner Sikkerhedscenter, kun anbefalinger og beskeder, der er relateret til ressourcer, brugeren har adgang til kan ses. Det betyder, at brugerne kun får vist elementer, der er relateret til ressourcer, hvor brugeren er tildelt rollen som ejer, bidragyder eller læser abonnement eller ressourcegruppe, en ressource tilhører.

Hvis du vil:

- **Redigere en sikkerhedspolitik**, skal du være en ejer eller bidragyder af abonnementet.
- **Anvend en anbefaling**, du skal være en ejer eller bidragyder af abonnementet.
- **Har indsigt i tilstanden sikkerhed på tværs af alle dine abonnementer**, skal du være en ejer, bidragyder eller læser (IT-administrator, Security Team) af hvert abonnement.
- **Har indsigt i tilstanden sikkerhed over dine ressourcer**, du skal være en ressourcegruppe ejer, bidragyder eller læser (DevOps).

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Hvilke Azure ressourcer overvåges af Azure Sikkerhedscenter?
Azure Sikkerhedscenter overvåger Azure følgende ressourcer:

- Virtuelle maskiner (VM'er) (herunder [Cloud Services](../cloud-services/cloud-services-choose-me.md))
- Azure virtuelle netværk
- Azure SQL-tjeneste
- Partnerløsninger integreret med abonnementet Azure som en firewall til webprogrammer på FOS og [App Service-miljø](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Virtuelle maskiner

### <a name="what-types-of-virtual-machines-will-be-supported"></a>Hvilke typer af virtuelle maskiner der skal understøttes?
Sikkerhed sundhedsovervågning og anbefalinger fås til virtuelle maskiner (VM'er), der er oprettet ved hjælp af både [Klassisk og ressourcestyring installation modeller](../azure-classic-rm.md).

Understøttede Windows FOS:

- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

Understøttede Linux FOS:

- Ubuntu versioner 12.04, 14.04, 16.04
- Debian versioner 7, 8
- CentOS versioner 6. \*, 7.*
- Rød Hat Enterprise Linux (RHEL) versioner 6. \*, 7.*
- SUSE Linux Enterprise Server (SLES) versioner 11. \*, 12.*
- Oracle Linux versioner 6. \*, 7.*

VM'er, der kører i en skybaseret tjeneste er også understøttet. Kun cloud services internettet og arbejder roller, der kører i fremstilling pladser overvåges. Hvis du vil vide mere om skybaseret tjeneste, du se [Oversigt over Cloud Services](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Hvorfor ikke Azure Sikkerhedscenter genkender Antimalwareprogram løsningen kører på min Azure VM?

Azure Sikkerhedscenter har kun indsigt i Antimalwareprogram installeret via Azure filtypenavne. For eksempel er Sikkerhedscenter ikke kunne registrere Antimalwareprogram, der var forudinstalleret på et billede, du har angivet, eller hvis du har installeret Antimalwareprogram på din virtuelle maskiner ved hjælp af dine egne processer (såsom systemer til konfiguration af administration).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Hvorfor får jeg meddelelsen "Manglende scanne Data" for min VM?

Det kan tage lidt tid (normalt er mindre end en time) for at udfylde når dataindsamling er aktiveret i Azure Sikkerhedscenter scanningsdata. Søgninger udfylder ikke for FOS i en standset tilstand.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Hvorfor får jeg vist meddelelsen "VM Agent er manglende?"

VM Agent skal være installeret på FOS for at aktivere dataindsamling. VM Agent er installeret som standard for VM'er, der er implementeret fra Azure Marketplace. Du kan finde oplysninger om, hvordan du installerer VM Agent på andre FOS blogindlægget [VM Agent og udvidelser](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
