<properties
   pageTitle="Oversigt over Azure virtuelle maskiner sikkerhed | Microsoft Azure"
   description=" Azure virtuelle maskiner giver dig fleksibilitet af virtualization uden at købe og vedligeholde den fysiske hardware, der kører den virtuelle maskine.  I denne artikel giver et overblik over grundlæggende Azure sikkerhedsfunktioner, der kan bruges med virtuelle Azure-computere. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Oversigt over Azure virtuelle maskiner-sikkerhed

Azure virtuelle maskiner kan du installere en lang række computing løsninger i en fleksible måde. Du kan installere en hvilken som helst arbejdsbelastningen og et sprog på næsten alle operativsystemet med understøttelse af Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP og Azure BizTalk-tjenester.

En Azure virtuelt giver dig mulighed for virtualization uden at købe og vedligeholde den fysiske hardware, der kører den virtuelle maskine.  Du kan opbygge og anvende dine programmer med assurance, at dine data er beskyttet og sikkert i vores meget sikre datacentre.

Med Azure, kan du bygge forbedret sikkerhed, overensstemmende løsninger, der:

- Beskytte din virtuelle maskiner mod virus og malware
- Kryptere dine følsomme data
- Sikre netværkstrafik
- Identificere og registrere trusler
- Overholde krav

Mål i denne artikel er at tilvejebringe et overblik over grundlæggende Azure sikkerhedsfunktioner, der kan bruges med virtuelle maskiner. Vi giver også links til artikler, som giver oplysninger om hver funktion, så du kan få mere at vide.  

De grundlæggende Azure virtuelt sikkerhedsfunktioner skal gennemgås i denne artikel:

- Antimalwareprogram
- Hardware sikkerhedsmodul
- Virtuelt disk kryptering
- Virtuelt sikkerhedskopi
- Gendannelse af Azure websteder
- Virtuelt netværk
- Administration af sikkerhedspolitik og -rapportering
- Overholdelse af angivne standarder

## <a name="antimalware"></a>Antimalwareprogram

Du kan bruge Antimalwareprogram software fra sikkerhed leverandører som Microsoft, Symantec, Trend Micro, McAfee og Kaspersky med Azure, du kan beskytte din virtuelle maskiner mod skadelig filer, adware og andre trusler. I afsnittet få mere at vide nedenfor for at finde artikler på partner løsninger.

Microsoft Antimalwareprogram til Azure Cloud Services og virtuelle maskiner er en beskyttelse i realtid funktion, der hjælper med at identificere og fjerne virus, spyware og anden ondsindet software.  Microsoft Antimalwareprogram indeholder konfigurerbar en besked, når kendte skadelig eller uønsket software forsøger at installere sig selv eller køre på dine Azure systemer.

Microsoft Antimalwareprogram er en enkelt agent løsning til programmer og lejer miljøerne, udviklet til at køre i baggrunden uden human brugerinput. Du kan installere beskyttelse baseret på behovene i din arbejdsbelastninger, med enten grundlæggende secure-ved-standard- eller avancerede brugerdefineret konfiguration, herunder Antimalwareprogram overvågning.

Når du installere og aktivere Microsoft Antimalwareprogram, findes de følgende hovedfunktioner:

- Beskyttelse i realtid - skærme aktivitet i Skytjenester og på virtuelle maskiner til at registrere og blokere udførelse af malware.
- Planlagt scanning - udfører med jævne mellemrum målrettede scanning at registrere malware, herunder aktivt kørende programmer.
- Malware afhjælpning - formateres automatisk handling på fundet malware, som hvis du sletter eller quarantining skadelige filer og oprydning af poster i skadelig registreringsdatabasen.
- Signaturopdateringer - automatisk installationer beskyttelse seneste signaturer (virusdefinitioner) til at sikre beskyttelse er opdateret på et forudbestemt frekvens.
- Antimalwareprogram program opdaterer – automatisk opdaterer Microsoft Antimalwareprogram-program.
- Antimalwareprogram Platform opdateringer – automatisk opdaterer Microsoft Antimalwareprogram platform.
- Aktive beskyttelse - rapporter til Azure telemetri metadata om fundet trusler og mistænkelige ressourcer for at sikre hurtige svar og aktiverer realtid synkron signatur levering gennem de Microsoft aktive beskyttelse System (kort).
- Eksempler reporting - indeholder og rapporter eksempler til tjenesten Microsoft Antimalwareprogram til at forfine tjenesten og aktivere fejlfinding.
- Udeladelser – gør det muligt for programmet på computeren og tjenesteadministratorer for at konfigurere bestemte filer, processer, og indfører udelade dem fra beskyttelse og scanning til ydeevne og andre årsager.
- Indsamling af Antimalwareprogram hændelser - registrerer Antimalwareprogram tjenestetilstand, mistænkelige aktiviteter og afhjælpning handlinger, der udføres i hændelseslog operativsystem og samler dem i en kundekonto Azure-lager.

Få mere at vide: Du kan få mere at vide om Antimalwareprogram software til at beskytte din virtuelle maskiner,:

- [Microsoft Antimalwareprogram til Azure Cloud Services og virtuelle maskiner](../security/azure-security-antimalware.md)
- [Implementering af Antimalwareprogram løsninger på Azure virtuelle maskiner](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Sådan installerer og konfigurerer Trend Micro Deep Security som en tjeneste på en Windows-VM](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Sådan installerer og konfigurerer Symantec Endpoint Protection på en Windows-VM](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nye Antimalwareprogram indstillinger for beskyttelse af Azure virtuelle maskiner – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Sikkerhedsløsninger i Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Hardware sikkerhed modul

Kryptering og godkendelse forbedrer ikke sikkerhed, medmindre de pågældende taster selv er beskyttet. Du kan forenkle administration og sikkerheden for dine vigtige hemmeligheder og nøgler ved at gemme dem i Azure-tasten samling. Tasten samling giver mulighed for at gemme dine nøgler i hardware sikkerhed moduler (HSMs), der er certificeret til at FIPS 140-2 niveau 2 standarder. Dine SQL Server-kryptering taster til sikkerhedskopiering eller [kryptering af gennemsigtig data](https://msdn.microsoft.com/library/bb934049.aspx) kan alle blive gemt i nøgle samling med taster eller hemmeligheder fra dine programmer. Tilladelser og adgang til disse beskyttede elementer administreres via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Lær mere:

- [Hvad er Azure-tasten samling?](../key-vault/key-vault-whatis.md)
- [Introduktion til Azure-tasten samling](../key-vault/key-vault-get-started.md)
- [Azure-tasten samling blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuelt disk kryptering

Azure Disk kryptering er en ny funktion, hvor du kan kryptere Windows og Linux Azure Virtual Machine disken. Azure Disk kryptering bruges funktionen branche standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) i Windows og funktionen [dm crypt](https://en.wikipedia.org/wiki/Dm-crypt) af Linux til at levere lydstyrken kryptering til Operativsystemet og datadisce.

Løsningen er integreret med Azure-tasten samling kan hjælpe dig med at kontrollere og administrere disk krypteringsnøgler og hemmeligheder i abonnementet vigtige samling og sikre, at alle data i virtuelt diskene er krypteret på resten i dit Azure-lager.

Lær mere:

- [Azure Disk kryptering til Windows og Linux IaaS FOS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Azure Disk kryptering for Linux og Windows virtuelle maskiner](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [Kryptere en virtuel maskine](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Virtuelt sikkerhedskopi

Azure sikkerhedskopi er en SVG løsning, der beskytter programdataene med nul kapital investering og minimale løbende omkostninger. Programfejl kan ødelægge dine data, og human fejl kan en fejl i dine programmer. Din virtuelle maskiner, der kører Windows og Linux er beskyttet med Azure sikkerhedskopiering.

Lær mere:

- [Hvad er Azure sikkerhedskopi?](../backup/backup-introduction-to-azure-backup.md)
- [Azure sikkerhedskopiering læringssti](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Azure sikkerhedskopiering Service - ofte stillede spørgsmål](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Gendannelse af Azure websteder

En vigtig del af organisationens BCDR strategi forståelse af hvordan du kan holde virksomhedens arbejdsmængder og apps og kører, når der optræder planlagte og ikke-planlagt afbrydelser. Azure gendannelse af websteder kan dirigerer gentagelse, failover og gendannelse af arbejdsmængder og apps, så de er tilgængelige fra en sekundær placering Hvis din primære placering går ned.

Gendannelse af websteder:

- **Simplificering din strategi for BCDR** – gendannelse af websteder gør det nemt at håndtere gentagelse, failover og gendannelse af flere business arbejdsmængder og apps fra ét sted. Gendannelse af websteder orchestrates gentagelse og belastningsjustering, men ikke SKÆRING programmets data eller få oplysninger om den.
- **Giver fleksible gentagelse** – ved hjælp af gendannelse af websteder kan du gentage arbejdsbelastninger, som kører på Hyper-V virtuelle maskiner, VMware virtuelle computere og Windows/Linux fysiske servere.
- **Understøtter failover og gendannelse** – gendannelse af websteder indeholder test failover for at understøtte nedbrud gendannelse drills uden at påvirke fremstilling miljøer. Du kan også køre planlagte failover med nul datatab forventede afbrydelser eller ikke-planlagt failover med minimale datatab (afhængigt af replikering frekvens) til uventede nedbrud. Når du failover kan du failback til din primære websteder. Gendannelse af websteder indeholder gendannelse planer, som kan indeholde scripts og Azure automatisering projektmapper, så du kan tilpasse failover og gendannelse af med flere lag programmer.
- **Overflødiggør sekundær datacenter** – du kan gentage en sekundær lokalt websted eller Azure. Brug af Azure som en destination til nedbrud fjerner omkostningerne og kompleksiteten af vedligeholde en sekundær websted. Replikerede data er gemt i Azure-lager.
- **Integrates med eksisterende BCDR teknologier** – gendannelse af websteder samarbejder med andre BCDR programfunktioner. Du kan for eksempel bruge gendannelse af websteder til at beskytte SQL Server back-end i virksomhedens arbejdsmængder. Dette omfatter indbygget understøttelse af SQL Server AlwaysOn til at administrere sekundære af tilgængelighed grupper.

Lær mere:

- [Hvad er Azure gendannelse af websteder?](../site-recovery/site-recovery-overview.md)
- [Hvordan fungerer gendannelse af Azure websteder?](../site-recovery/site-recovery-components.md)
- [Hvad arbejdsbelastninger, som er beskyttet af Azure gendannelse af websteder?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuelt netværk

Virtuelle maskiner skal netværksforbindelse. For at understøtte dette krav, kræver Azure virtuelle maskiner have forbindelse til et virtuelt Azure-netværk. Et virtuelt Azure-netværket ikke er en logisk konstruktion indbygget oven på fysiske Azure netværk-strukturen. Hver logiske Azure virtuelt netværk er adskilt fra alle andre Azure virtuelle netværk. Denne isolation hjælper med at sikre, at netværkstrafik i dine installationer ikke er tilgængeligt for andre Microsoft Azure-kunder.

Lær mere:

- [Oversigt over Azure netværk sikkerhed](security-network-overview.md)
- [Oversigt over virtuelt netværk](../virtual-network/virtual-networks-overview.md)
- [Netværksfunktioner og partnerskaber for Enterprise-scenarier](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Administration af sikkerhedspolitik og -rapportering

Azure Sikkerhedscenter kan du forhindre, at registrere og besvare trusler og giver du større indsigt i, og kontrol over, sikkerheden for dine Azure ressourcer. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

Azure Sikkerhedscenter hjælper dig med at optimere og overvåge virtuelt sikkerhed ved at:

- Give virtuelt [sikkerhedsanbefalinger](../security-center/security-center-recommendations.md) såsom Anvend systemopdateringer, konfigurere ACLs slutpunkter, aktivere Antimalwareprogram, aktivere netværk sikkerhedsgrupper og anvende disk kryptering.
- Overvågning af virtuelle maskiner tilstand

Lær mere:

- [Introduktion til Azure Security Center](../security-center/security-center-intro.md)
- [Azure Sikkerhedscenter stillede ofte spørgsmål](../security-center/security-center-faq.md)
- [Azure Sikkerhedscenter planlægning og handlinger](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Overholdelse af angivne standarder

Azure virtuelle maskiner er certificeret til FISMA, FedRAMP, HIPAA, PCI DSS niveau 1 og andre vigtige overholdelse programmer. Denne certificering gør det nemmere for din egen Azure-programmer til at overholde krav og for din virksomhed til en adresse til en bred vifte af interne og eksterne lovmæssige krav.

Lær mere:

- [Microsoft Sikkerhedscenter: overholdelse af angivne standarder](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Der er tillid til skyen: Microsoft Azure sikkerhed, beskyttelse af personlige oplysninger og overholdelse](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
