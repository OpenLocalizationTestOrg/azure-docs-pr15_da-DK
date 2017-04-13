<properties
   pageTitle="Azure Sikkerhedscenter datasikkerhed | Microsoft Azure"
   description="Dette dokument forklares, hvordan data administreres og sikres i Azure Sikkerhedscenter."
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
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-data-security"></a>Azure Sikkerhedscenter datasikkerhed
Ned for at hjælpe kunder forhindre, registrerer og besvare trusler, Azure Sikkerhedscenter indsamler og behandler data om dine Azure ressourcer, herunder oplysninger om konfiguration, metadata, hændelseslogfiler, lagring af filer og meget mere. Vi yder stærke forpligtelser til at beskytte fortroligheden og sikkerheden for disse data. Microsoft overholder faste retningslinjerne for overholdelse af regler og sikkerhed for – fra koder til operativsystem en tjeneste. 

I denne artikel beskrives, hvordan data administreres og sikres i Azure Sikkerhedscenter.

## <a name="data-sources"></a>Datakilder

Azure Sikkerhedscenter analyserer data fra følgende kilder:

- Azure tjenester: Læser oplysninger om konfiguration af Azure tjenester, du har installeret ved at kommunikere med denne tjeneste ressource udbyder.
- Netværkstrafik: Læser prøver fra netværk trafik metadata fra Microsoft-infrastruktur, som kilde/destination IP-/ port, pakkestørrelse og netværksprotokol.
- Partnerløsninger: Indsamler sikkerhedsadvarsler fra integreret partnerløsninger, såsom firewalls og Antimalwareprogram løsninger. Disse data er gemt i Azure Sikkerhedscenter lagerplads, der aktuelt er placeret i USA.
- Din virtuelle maskiner: Azure Sikkerhedscenter kan indsamler oplysninger om konfiguration og oplysninger om sikkerhed begivenheder, som Windows begivenhed og overvåge logfiler, IIS logfiler, syslog meddelelser og nedbrud lagring af filer fra din virtuelle maskiner ved hjælp af brugere til indsamling af data. Se afsnittet "Administrere dataindsamling" nedenfor for at få mere at vide.  

Desuden er oplysninger om sikkerhedsadvarsler, anbefalinger og sikkerhed sundhedsstatus gemt i Azure Sikkerhedscenter lagerplads, der aktuelt er placeret i USA. Disse oplysninger kan omfatte relaterede konfigurationsoplysninger og sikkerhedshændelser, der indsamles fra din virtuelle maskiner efter behov for at give dig den sikkerhedsmeddelelsen, anbefaling eller sikkerhed sundhedsstatus.

## <a name="data-protection"></a>Beskytte data

**Data opdeling**: Data bevares logisk separat på hver komponent i hele tjenesten. Alle data, der er mærket i organisationen. Denne mærkning fortsætter i hele livscyklus data, og den håndhæves hvert enkelt lag til tjenesten. Desuden er data, der indsamles fra din virtuelle maskiner gemt i din lagerplads konti.

**Dataadgang**: for at give anbefalinger til sikkerhed, og Undersøg potentielle sikkerhedsrisici, Microsoft personale kan få adgang til oplysninger der indsamles eller analysere Azure-tjenester, herunder nedbrud lagring af filer. Nedbrud lagring af filer og proces oprettelse af begivenheder kan utilsigtet indeholde kundedata eller personlige data fra din virtuelle computere. Vi overholde [Microsoft Online Services betingelser](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) og [Erklæring om](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), hvilke tilstand, at Microsoft ikke bruge kundedata eller udlede oplysninger fra den til reklame eller lignende kommerciel brug. Vi kun bruge kundedata efter behov for at give dig Azure tjenesterne, herunder formål, der er kompatible med give disse tjenester. Du bevarer alle rettigheder til kundedata.

**Brug af data**: Microsoft bruger mønstre og truslen intelligence set på tværs af flere lejere for at forbedre vores at forebygge og opdage funktionalitet Vi kan gøre det i overensstemmelse med beskrevet i vores [Erklæring om](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)beskyttelse af personlige oplysninger forpligtelser.

**Placering af**: en lagerplads konto er angivet for hvert område, hvor virtuelle computere der kører. Dette gør det muligt at gemme data i det samme område som den virtuelle maskine, hvor dataene er indsamlet. Disse data, herunder nedbrud gemte filer gemmes vedvarende i kontoen lagerplads. Tjenesten indeholder også oplysninger om sikkerhedsadvarsler, herunder beskeder fra integreret partnerløsninger, anbefalinger og sikkerhed sundhedsstatus i Azure Sikkerhedscenter lagerplads, der aktuelt er placeret i USA.

## <a name="managing-data-collection-from-virtual-machines"></a>Administrere dataindsamling fra virtuelle maskiner

Når du vælger at aktivere Azure Sikkerhedscenter, er dataindsamling slået til for hver af dine abonnementer. Du kan deaktivere dataindsamling i afsnittet "Sikkerhedspolitik" i dashboardet Azure Security Center. Når dataindsamling er slået til, understøttes Azure Sikkerhedscenter bestemmelser Azure overvågning Agent på alle eksisterende virtuelle maskiner og eventuelle nye, der er oprettet. Filtypenavnet Azure sikkerhed overvågning scanner for forskellige sikkerhedsrelaterede konfigurationer og begivenheder sporer dem til [Begivenhed sporing til Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Desuden hæve operativsystemet hændelseslog begivenheder i løbet af kører maskinen. Eksempler på sådanne data er: operativsystemtype og version, fungerer systemets logfiler (Windows-hændelseslogge), kører processer, computernavn, IP-adresser, der er logget på bruger og lejer-ID. Azure overvågning Agent læser hændelseslogposter og ETW sporer og kopierer dem til kontoen lagerplads til analyse. 

En lagerplads konto er angivet for hvert område, hvor du har virtuelle maskiner, der kører, hvor data indsamles fra virtuelle maskiner, der er gemt samme område. Det gør det nemt for dig at bevare data i det samme geografiske område til beskyttelse af personlige oplysninger og data højhedsområde formål. Du kan konfigurere lagerplads konti for hvert område i afsnittet "Sikkerhedspolitik" i dashboardet Azure Security Center.

Azure overvågning Agent kopierer også nedbrud lagring af filer til kontoen lagerplads.  Azure Sikkerhedscenter indsamler kortvarige kopier af dine nedbrud lagring af filer og analyserer dem efter tegn på udnytte forsøg og vellykket kompromiser.  Azure Sikkerhedscenter udfører denne analyse i det samme geografiske område som kontoen lager og sletter kortvarige kopier, når analysen er fuldført.

Du kan deaktivere dataindsamling fra virtuelle maskiner til enhver tid, som fjerner alle overvågning supportmedarbejdere tidligere installeret af Azure Sikkerhedscenter.


## <a name="see-also"></a>Se også

I dette dokument, du har lært hvordan data administreres og sikres i Azure Sikkerhedscenter. Hvis du vil vide mere om Azure Sikkerhedscenter, i:

- [Azure Security Center planlægning og Operations Guide](security-center-planning-and-operations-guide.md) – Lær, hvordan du planlægger og forstå Designovervejelser at indføre Azure Sikkerhedscenter.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for ressourcerne Azure
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed
- [Overvågning partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten
- [Azure sikkerhed Blog](http://blogs.msdn.com/b/azuresecurity/) – finde blogindlæg om Azure sikkerhed og overholdelse
