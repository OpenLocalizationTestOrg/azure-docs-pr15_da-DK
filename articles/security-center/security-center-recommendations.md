<properties
   pageTitle="Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument vejleder dig gennem hvordan anbefalinger i Azure Sikkerhedscenter hjælper dig med at beskytte dine Azure ressourcer og holde i overensstemmelse med sikkerhedspolitikker."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="managing-security-recommendations-in-azure-security-center"></a>Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter

Dette dokument vejleder dig gennem Sådan bruges anbefalinger i Azure Security Center til at hjælpe dig med at beskytte dine Azure ressourcer.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="what-are-security-recommendations"></a>Hvad er sikkerhedsanbefalinger?
Sikkerhedscenter analyserer med jævne mellemrum tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, opretter anbefalinger. Anbefalinger fører dig gennem processen med at konfigurere nødvendige kontrolelementer.

## <a name="implementing-security-recommendations"></a>Implementere sikkerhedsanbefalinger

### <a name="set-recommendations"></a>Sæt anbefalinger

I [indstilling sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md), kan du lære at:

- Konfigurere sikkerhedspolitikker.
- Aktivere dataindsamling.
- Vælg hvilken anbefalinger for at få vist som en del af din sikkerhedspolitik.

Aktuelle politik anbefalinger center rundt om opdateringer, oprindelige regler, Antimalwareprogram programmer, [netværk sikkerhedsgrupper](../virtual-network/virtual-networks-nsg.md) på undernet og netværksgrænseflader, SQL database overvågning, SQL-database gennemsigtig datakryptering og web application firewalls.  [Angive sikkerhedspolitikker](security-center-policies.md) indeholder en beskrivelse af hver enkelt indstilling for anbefaling.

### <a name="monitor-recommendations"></a>Overvåge anbefalinger
Når du har indstillet en sikkerhedspolitik, analyserer Sikkerhedscenter sikkerhed tilstanden for dine ressourcer til at identificere potentielle svagheder. Feltet **anbefalinger** på bladet **Sikkerhedscenter** indikerer, at det samlede antal anbefalinger, der er identificeret med Sikkerhedscenter.

![Feltet anbefalinger][1]

For at se detaljerne for hver anbefaling:

1. Klik på den **anbefalinger flisen** på bladet **Sikkerhedscenter** . Bladet **anbefalinger** åbnes.

Anbefalinger er vist i et tabelformat, hvor hver linje repræsenterer en bestemt anbefaling. Kolonnerne i denne tabel er:

- **Beskrivelse**: forklarer anbefalingen, og hvad der skal udføres for at løse den.
- **Ressource**: Viser de ressourcer, som denne anbefaling gælder.
- **Staten**: beskriver den aktuelle tilstand for anbefalingen:
    - **Åbn**: anbefalingen endnu ikke er blevet behandlet.
    - **I gang**: anbefalingen aktuelt anvendes på ressourcerne, og ingen handling er påkrævet af dig.
    - **Løst**: anbefalingen er blevet fuldført (i dette tilfælde linjen vil være nedtonet).
- **ALVORLIGHED**: beskrives alvorlighed af pågældende bestemt anbefaling:
    - **Høj**: en sikkerhedsrisiko findes med en beskrivende ressource (som et program, en VM eller en sikkerhedsgruppe netværk) og kræver opmærksomhed.
    - **Mediet**: en sikkerhedsrisiko og ikke-kritisk eller eventuelle yderligere trin er påkrævet, at fjerne den eller for at fuldføre en proces.
    - **Lav**: en sikkerhedsrisiko, der skal løses, men ikke kræver øjeblikkelig handling. (Som standard lav anbefalinger vises ikke, men du kan filtrere på lav anbefalinger, hvis du vil se dem).

Brug tabellen nedenfor som en reference til at hjælpe dig med at forstå de tilgængelige anbefalinger, og hvad hver enkelt vil gøre, hvis du har anvendt den.

> [AZURE.NOTE] Du skal forstå [Klassisk og ressourcestyring installation modeller](../azure-classic-rm.md) for Azure ressourcer.

|Anbefaling|Beskrivelse|
|-----|-----|
|[Aktivere dataindsamling for abonnementer](security-center-enable-data-collection.md)|Anbefaler, at du slår dataindsamling i sikkerhedspolitikken for hver af dine abonnementer og alle virtuelle maskiner (VM'er) i dine abonnementer.|
|[Afhjælpning OS svagheder](security-center-remediate-os-vulnerabilities.md)|Anbefaler, at du justere din OS konfigurationer med regler anbefalet konfiguration tillader fx ikke adgangskoder skal gemmes.|
|[Anvende systemopdateringer](security-center-apply-system-updates.md)|Anbefaler, at du installerer manglende system sikkerhedsopdateringer og vigtige opdateringer til FOS.|
|[Genstart efter opdateringer](security-center-apply-system-updates.md#reboot-after-system-updates)|Anbefaler, at du genstarter en VM for at fuldføre processen med at anvende systemopdateringer.|
|[Tilføje en firewall til webprogrammer](security-center-add-web-application-firewall.md)|Anbefaler, at du installerer en firewall til webprogrammer (WAF) for web slutpunkter. Du kan beskytte flere webprogrammer i Sikkerhedscenter ved at tilføje disse programmer til din eksisterende WAF installationer. WAF anvendelser (oprettet ved hjælp af Ressourcestyring implementeringsmodel) skal være installeret på et separat virtuelt netværk. WAF anvendelser (oprettet ved hjælp af den klassiske implementeringsmodel) er begrænset til at bruge en netværk sikkerhedsgruppe. Denne understøttelse udvides til en fuldt tilpassede installation af en WAF maskinen (klassisk) i fremtiden. Sikkerhedscenter anbefaler, at du klargør et WAF for at beskytte mod angreb målretning af dine webprogrammer på FOS og på App-tjenesten-miljø (ASE). Hvis du vil vide mere om ASE skal du i [App Servicedokumentationen miljø](../app-service/app-service-app-service-environments-readme.md). |
|[Færdiggøre programmet beskyttelse](security-center-add-web-application-firewall.md#finalize-application-protection)|For at fuldføre konfigurationen af en WAF skal blive trafik omdirigeret til WAF maskinen. Følge denne anbefaling fuldfører konfiguration af nødvendige ændringer.|
|[Tilføje en næste generering af Firewall](security-center-add-next-generation-firewall.md)|Anbefaler, at du føjer en næste generering af Firewall (NGFW) fra en Microsoft-partner for at øge din sikkerhed beskyttelse.|
|[Distribuere-trafik gennem NGFW kun](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Anbefaler, at du konfigurerer netværk sikkerhed gruppe (NSG) regler, der tvinger indgående trafik til dit VM gennem din NGFW.|
|[Installere Endpoint Protection](security-center-install-endpoint-protection.md)|Anbefaler, at du klargør Antimalwareprogram programmer til at FOS (kun Windows FOS).|
|[Løse Endpoint Protection sundhed beskeder](security-center-resolve-endpoint-protection-health-alerts.md)|Anbefaler, at du kan løse slutpunkt beskyttelse fejl.|
|[Aktivere netværk sikkerhedsgrupper på undernet eller virtuelle maskiner](security-center-enable-network-security-groups.md)|Anbefaler, at du aktiverer NSGs på undernet eller FOS.|
|[Begrænse adgang via internettet modstående slutpunkt](security-center-restrict-access-through-internet-facing-endpoints.md)|Anbefaler, at du konfigurerer indgående trafikregler for NSGs.|
|[Aktivere overvågning af SQL-server](security-center-enable-auditing-on-sql-servers.md)|Anbefaler, at du slår overvågning for Azure SQL-servere (SQL Azure service kun; ikke omfatter SQL, der kører på din virtuelle maskiner).|
|[Aktivere overvågning af SQL-database](security-center-enable-auditing-on-sql-databases.md)|Anbefaler, at du slår overvågning for Azure SQL-databaser (SQL Azure service kun; ikke omfatter SQL, der kører på din virtuelle maskiner).|
|[Aktivere gennemsigtig kryptering af Data på SQL-databaser](security-center-enable-transparent-data-encryption.md)|Anbefaler, at du aktiverer kryptering for SQL-databaser (kun SQL Azure service).|
|[Aktivere VM Agent](security-center-enable-vm-agent.md)|Gør det muligt at se, hvilke FOS kræver VM Agent. VM Agent skal være installeret på FOS for at klargøre programrettelse scanning, Oprindelig plan scanning og Antimalwareprogram programmer. VM Agent er installeret som standard for VM'er, der er implementeret fra Azure Marketplace. I artiklen [VM Agent og udvidelser – del 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) indeholder oplysninger om hvordan du installerer VM Agent.|
| [Anvende disk kryptering](security-center-apply-disk-encryption.md) |Anbefaler, at du krypterer VM disken ved hjælp af Azure Disk kryptering (Windows og Linux VM'er). Kryptering anbefales til både OS og enheder på din VM.|
|[Angive sikkerhed kontaktoplysninger](security-center-provide-security-contact-details.md) | Anbefaler, at du angiver sikkerhed kontaktoplysninger for hver af dine abonnementer. Kontaktoplysninger er en e-mail-adresse og dit telefonnummer. Oplysninger, der bruges til at kontakte dig, hvis vores sikkerhed team finder, ressourcerne er afsløret. |
| [Opdatere OS-version](security-center-update-os-version.md) | Anbefaler, at du opdaterer operativsystemversion (OS) til din skybaseret tjeneste til den nyeste version, der er tilgængelige for din OS familie.  Hvis du vil vide mere om Cloud Services, få vist [Cloud Services oversigt](../cloud-services/cloud-services-choose-me.md). |
| [Sikkerhedsrisiko vurdering ikke er installeret](security-center-vulnerability-assessment-recommendations.md) | Anbefaler, at du installerer en sikkerhedsrisiko vurdering løsning på din VM. |
| [Afhjælpning svagheder](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Gør det muligt at se system- og svagheder, der er fundet af sikkerhedsrisiko vurdering løsningen installeret på din VM. |

Du kan filtrere og annullere anbefalinger.

1. Klik på **Filter** på bladet **anbefalinger** . Bladet **Filter** åbnes, og du vælger de alvor og stat værdier, du vil have vist.

    ![Filtrere anbefalinger][2]

2. Hvis du beslutter, at en anbefaling ikke er tilgængelig, kan du annullere anbefalingen og filtrere den ud af visningen. Der er to måder at afvise en anbefaling. En metode er at højreklikke på et element, og vælg derefter **Afvis**. Den anden er ved at pege på et element, skal du klikke på de tre prikker, der vises til højre, og vælg derefter **Afvis**. Du kan få vist afskedigede anbefalinger ved at klikke på **Filter**, og derefter vælge **Dismissed**.

    ![Afvise anbefaling][3]

### <a name="apply-recommendations"></a>Anvende anbefalinger
Beslutte, du skal anvende først, når du har gennemset alle anbefalinger. Vi anbefaler, at du bruger klassifikationen, som den primære parameter til at evaluere hvilke anbefalinger skal anvendes først.

Vælg en anbefaling i tabellen ovenfor anbefalinger, og gennemgå den som et eksempel på, hvordan du anvender en anbefaling.

## <a name="see-also"></a>Se også
I dette dokument, blev du introduceret til anbefalinger til sikkerhed i Sikkerhedscenter. Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågning partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – finde blogindlæg om Azure sikkerhed og overholdelse af regler.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
