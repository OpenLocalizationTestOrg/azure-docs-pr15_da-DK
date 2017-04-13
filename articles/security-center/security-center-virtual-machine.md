<properties
   pageTitle="Azure Sikkerhedscenter og Azure virtuelle maskiner | Microsoft Azure"
   description="Dette dokument hjælper dig med at forstå, hvordan Azure Sikkerhedscenter kan beskytte din virtuelle Azure-computere."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Azure Sikkerhedscenter og Azure virtuelle maskiner

[Azure Sikkerhedscenter](https://azure.microsoft.com/services/security-center/) hjælper dig med at forhindre, at registrere og besvare trusler. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

I denne artikel beskrives, hvordan Sikkerhedscenter kan hjælpe dig med at sikre dine Azure virtuelle maskiner (VM).

## <a name="why-use-security-center"></a>Hvorfor bruge Sikkerhedscenter?

Sikkerhedscenter hjælper dig med at beskytte virtuelt data i Azure ved at indsende indsigt i din virtuelle maskine sikkerhedsindstillinger. Når Sikkerhedscenter beskytter dine FOS, fås følgende funktioner:

- Operativsystem (OS) sikkerhedsindstillinger med de anbefalede konfigurationsregler
- System sikkerhedsopdateringer og vigtige opdateringer, der mangler
- Endpoint protection anbefalinger
- Disken kryptering validering
- Sikkerhedsrisiko vurdering og afhjælpning
- Truslen registrering

Ud over at beskytte din Azure FOS, giver Sikkerhedscenter også sikkerhed overvågning og administration for Cloud Services, App Services, virtuelle netværk og meget mere. 

>[AZURE.NOTE] Se [Introduktion til Azure Sikkerhedscenter](security-center-intro.md) mere at vide om Azure Sikkerhedscenter.

## <a name="prerequisites"></a>Forudsætninger

For at komme i gang med Azure Security Center, skal du kender og skal du overveje følgende:

- Du skal have et abonnement på Microsoft Azure. Se [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/) for flere oplysninger om Security Center gratis og standardversionen niveauer.
- Planlægge din Sikkerhedscenter indføring, skal du se [Azure Sikkerhedscenter planlægning og drift vejledning](security-center-planning-and-operations-guide.md) til at få mere at vide om planlægning og drift overvejelser i forbindelse med.
- Du kan finde oplysninger om operativsystem supportability [Azure Sikkerhedscenter ofte stillede spørgsmål (FAQ)](security-center-faq.md). 

## <a name="set-security-policy"></a>Angive sikkerhedspolitik

Indsamling af data skal være aktiveret, så den Azure Sikkerhedscenter kan indsamle de oplysninger, de skal angive anbefalinger og påmindelser, der genereres baseret på den sikkerhedspolitik, du konfigurerer. I nedenstående figur, du kan se, **indsamling af Data** er blevet slået **til**.

En sikkerhedspolitik definerer de kontrolelementer, der er anbefalet til ressourcer inden for den angivne abonnement eller ressourcegruppe. Før du aktiverer sikkerhedspolitik, skal du have dataindsamling aktiveret, Sikkerhedscenter indsamler data fra din virtuelle maskiner for at vurdere deres sikkerhedstilstand, giver anbefalinger til sikkerhed, og giver dig besked om trusler. I Sikkerhedscenter definerer du politikker for din Azure abonnementer eller ressourcegrupper afhængigt af din virksomheds sikkerhedsbehov og typer programmer eller følsomheden for dataene i hvert abonnement. 

![Sikkerhedspolitik](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] Vil vide mere om hver **politik for beskyttelse mod** tilgængelig, se [konfigurere sikkerhedspolitikker](security-center-policies.md) artikel.

## <a name="manage-security-recommendations"></a>Administrere sikkerhedsanbefalinger

Sikkerhedscenter analyserer tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, opretter anbefalinger. Anbefalinger fører dig gennem processen med at konfigurere nødvendige kontrolelementer.

Når du har indstillet en sikkerhedspolitik, analyserer Sikkerhedscenter sikkerhed tilstanden for dine ressourcer til at identificere potentielle svagheder. Anbefalinger er vist i et tabelformat, hvor hver linje repræsenterer en bestemt anbefaling. Tabellen nedenfor indeholder nogle eksempler på anbefalinger til Azure FOS og hvad hver enkelt vil gøre, hvis du har anvendt den. Når du vælger en anbefaling, får du oplysninger, der viser, hvordan du implementerer anbefalingen i Sikkerhedscenter.

|Anbefaling|Beskrivelse|
|-----|-----|
|[Aktivere dataindsamling for abonnementer](security-center-enable-data-collection.md)|Anbefaler, at du slår dataindsamling i sikkerhedspolitikken for hver af dine abonnementer og alle virtuelle maskiner (VM'er) i dine abonnementer.|
|[Afhjælpning OS svagheder](security-center-remediate-os-vulnerabilities.md)|Anbefaler, at du justere din OS konfigurationer med regler anbefalet konfiguration tillader fx ikke adgangskoder skal gemmes.|
|[Anvende systemopdateringer](security-center-apply-system-updates.md)|Anbefaler, at du installerer manglende system sikkerhedsopdateringer og vigtige opdateringer til FOS.|
|[Genstart efter opdateringer](security-center-apply-system-updates.md#reboot-after-system-updates)|Anbefaler, at du genstarter en VM for at fuldføre processen med at anvende systemopdateringer.|
|[Installere Endpoint Protection](security-center-install-endpoint-protection.md)|Anbefaler, at du klargør Antimalwareprogram programmer til at FOS (kun Windows FOS).|
|[Løse Endpoint Protection sundhed beskeder](security-center-resolve-endpoint-protection-health-alerts.md)|Anbefaler, at du kan løse slutpunkt beskyttelse fejl.|
|[Aktivere VM Agent](security-center-enable-vm-agent.md)|Gør det muligt at se, hvilke FOS kræver VM Agent. VM Agent skal være installeret på FOS for at klargøre programrettelse scanning, Oprindelig plan scanning og Antimalwareprogram programmer. VM Agent er installeret som standard for VM'er, der er implementeret fra Azure Marketplace. I artiklen [VM Agent og udvidelser – del 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) indeholder oplysninger om hvordan du installerer VM Agent.|
| [Anvende disk kryptering](security-center-apply-disk-encryption.md) |Anbefaler, at du krypterer VM disken ved hjælp af Azure Disk kryptering (Windows og Linux VM'er). Kryptering anbefales til både OS og enheder på din VM.|
| [Sikkerhedsrisiko vurdering ikke er installeret](security-center-vulnerability-assessment-recommendations.md) | Anbefaler, at du installerer en sikkerhedsrisiko vurdering løsning på din VM. |
| [Afhjælpning svagheder](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Gør det muligt at se system- og svagheder, der er fundet af sikkerhedsrisiko vurdering løsningen installeret på din VM. |

>[AZURE.NOTE] Vil vide mere om anbefalinger, kan du [administrere sikkerhedsanbefalinger](security-center-recommendations.md) til artiklen.

## <a name="monitor-security-health"></a>Overvåge sikkerhed tilstand

Når du aktiverer [sikkerhedspolitikker](security-center-policies.md) for ressourcer, der er et abonnement, analysere Sikkerhedscenter sikkerheden for dine ressourcer til at identificere potentielle svagheder.  Du kan få vist tilstanden sikkerhed for ressourcerne sammen med eventuelle problemer i bladet **ressource sikkerhed tilstand** . Når du klikker på **virtuelle maskiner** i feltet **ressourcesikkerhed** tilstand, åbnes bladet **virtuelle maskiner** med anbefalinger til din FOS. 

![Sikkerhed tilstand](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Administrere og reagere på beskeder om sikkerhed

Sikkerhedscenter automatisk indsamler, analyserer og integrerer logdata fra Azure ressourcerne, netværk og forbundne partnerløsninger (som firewall og slutpunkt løsninger til beskyttelse), hvis du vil registrere reelle trusler og reducere forkerte forekomster. Sikkerhedscenter er ved at udnytte en forskelligartet sammenlægning af [registrering af funktioner](security-center-detection-capabilities.md), kan generere prioriterede sikkerhedsadvarsler for at hjælpe dig med hurtigt at undersøge problemet og give anbefalinger til, hvordan du afhjælpning mulige angreb.

![Sikkerhedsadvarsler](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Vælg en sikkerhedsmeddelelsen for at lære mere om de hændelser, som udløste den besked, og hvad, hvis et trin, du skal gennemføre for at afhjælpning et angreb. Sikkerhedsadvarsler er grupperet efter [type](security-center-alerts-type.md) og dato.


## <a name="see-also"></a>Se også

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
