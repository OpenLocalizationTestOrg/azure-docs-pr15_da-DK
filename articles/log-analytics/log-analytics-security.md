<properties
    pageTitle="Log Analytics datasikkerhed | Microsoft Azure"
    description="Få mere at vide om, hvordan Log Analytics beskytter dine personlige oplysninger og sikrer dine data."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="banders"/>

# <a name="log-analytics-data-security"></a>Log Analytics datasikkerhed

Microsoft er forpligtet til at beskytte dine personlige oplysninger og sikring af dine data, under fremvisning af software og -tjenester, der hjælper dig med at administrere IT-infrastruktur i din organisation. Vi genkendes, når du overdrage dine data til andre, kræver, sikkerhed og rettighedsadministration strenge sikkerhed. Microsoft overholder faste retningslinjerne for overholdelse af regler og sikkerhed for – fra koder til operativsystem en tjeneste.

Sikring af og beskyttelse af data er højeste prioritet hos Microsoft. Kontakt os, med en hvilken som helst spørgsmål, forslag eller problemer i forbindelse med en af følgende oplysninger, herunder vores sikkerhedspolitikker på [Azure supportmuligheder](http://azure.microsoft.com/support/options/).

I denne artikel beskrives, hvordan data indsamles, behandles og sikret ved Log Analytics i handlinger Management pakke (OMS). Du kan bruge supportmedarbejdere til at oprette forbindelse til webtjenesten, bruge System Center Operations Manager til at indsamle funktionsdygtige data eller hente data fra Azure diagnosticering til brug af Log analyser. De indsamlede data sendes via internettet ved hjælp af certifikat-baseret godkendelse og SSL 3 til Log Analytics-tjenesten, som er hostet i Microsoft Azure. Data er komprimeret ved agenten, før den er sendt.

Tjenesten Log Analytics administrerer din skybaserede data sikkert ved hjælp af følgende metoder:

- opdeling af data
- dataopbevaring
- fysisk sikkerhed
- Hændelsen administration
- overholdelse af angivne standarder
- sikkerhed i verdensklasse certificering


## <a name="data-segregation"></a>Opdeling af data

Kundedata opbevares logisk separat på hver komponent i hele for OMS-tjenesten. Alle data, der er mærket i organisationen. Denne mærkning fortsætter i hele livscyklus data, og den håndhæves hvert enkelt lag til tjenesten. Hver kunde har en dedikeret Azure blob, der indeholder de langsigtede data

## <a name="data-retention"></a>Dataopbevaring

Indekserede log søgedata gemmes og bevares ifølge din priser plan. Se [Log Analytics priser](https://azure.microsoft.com/pricing/details/log-analytics/)kan finde flere oplysninger.

Microsoft sletter kundedata 30 dage efter OMS arbejdsområdet er lukket. Microsoft også slette kontoen Azure-lager, hvor dataene opbevares. Når kundedata er fjernet, er ingen fysiske drev ødelagt.

I følgende tabel vises nogle af de tilgængelige løsninger i OMS og eksempler typerne data de indsamling.

| **Løsning** | **Datatyper** |
| --- | --- |
| Konfiguration af vurdering | Af konfigurationsdata, metadata og tilstand data |
| Planlægning af kapacitet | Ydeevnen data og metadata |
| Antimalwareprogram | Af konfigurationsdata og metadata |
| System opdatering vurdering | Metadata og stat data |
| Log Management | Brugerdefinerede hændelseslogfiler, Windows-hændelseslogge og/eller IIS-logfilerne |
| Registrering af ændringer | Software lager og Windows-tjenesten metadata |
| SQL- og Active Directory vurdering | Få vist resultaterne, WMI-data, data fra registreringsdatabasen, ydelsesdata og dynamisk administration af SQL Server |

I følgende tabel vises eksempler på datatyper:

| **Datatype** | **Felter** |
| --- | --- |
| Giv besked | Giv besked navn, beskrivelse af besked, BaseManagedEntityId, Problem-ID, IsMonitorAlert, RuleId, ResolutionState, prioritet, alvor, kategori, ejer, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguration | Kunde-id, AgentID, enheds-id, ManagedTypeID, ManagedTypePropertyID, Aktuelværdi, ChangeDate |
| Begivenhed | Begivenhedid EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, kategori, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, og TimeAdded <br>**Note:** Når du logger begivenheder med brugerdefinerede felter i Windows-hændelsesloggen, indsamler OMS dem. |
| Metadata | BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP-adresse, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-adresse, NetbiosDomainName, LogicalProcessors, DNSName, vist navn, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Ydeevne | Objektnavn, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stat | StateChangeEventId, StateId, NewHealthState, OldHealthState, kontekst, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysisk sikkerhed

Log analyser i OMS-tjenesten er bemandet med Microsoft personale og alle aktiviteter logføres og kan blive overvåget. Tjenesten kører helt i Azure og overholder Azure almindelige engineering kriterierne. Du kan få vist oplysninger om fysisk sikkerheden for Azure Aktiver på side 18 i [Oversigt over Microsoft Azure-sikkerhed](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysisk adgangsrettigheder til at sikre områder, der ændres i én arbejdsdag for alle, der ikke længere har ansvaret for tjenesten OMS, herunder overførsel og afslutning. Du kan læse om den globale fysisk infrastruktur vi bruge hos [Microsoft datacentre](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Hændelsen administration

OMS har en hændelsen management-proces, der overholder alle Microsoft-tjenester. Opsummere, vi:

- Bruge en fælles ansvar model, hvor en del af sikkerhed ansvar tilhører Microsoft, mens en del tilhører kunden
- Administrere Azure sikkerhedshændelser
  - Registrere en hændelse på den første angivelse til at starte en undersøgelse
  - Vurder effekt og alvorlighed af en hændelse ved et medlem af projektteamet på opkald hændelsen svar. Baseret på dokumentation, vurdering kan eller ikke kan medføre yderligere videresendelse til security response team.
  - Diagnosticere en hændelse af sikkerhed svar eksperter forestå tekniske eller sikkerhedsværktøj undersøgelsen, skal du identificere beholdning, afhjælpning og løsning strategier. Hvis sikkerhed teamet mener, at kundedata muligvis har bliver vises en ulovlig eller uautoriseret person, begynder parallel udførelse af processen kunde hændelse meddelelse parallelt.  
  - Stabilisere og gendanne fra hændelsen. Hændelsen svar teamet opretter en gendannelse for at reducere problemet. Forbindelse beholdning trin som quarantining påvirkede systemer kan opstå med det samme og parallelt med diagnosticering. Kan planlægges længere ord afhjælpninger som finder sted efter øjeblikkelig risikoen er passeret.  
  - Lukke hændelsen, og Foretag et indlæg syn. Hændelsen svar teamet opretter et indlæg syn, der viser oplysninger om hændelsen med formålet at revidere politikker, procedurer og processer for at forhindre en gentagelse af begivenheden.
- Give kunderne meddelelse om sikkerhedshændelser
  - Fastlægge projektets omfang af påvirkede kunder og angive alle påvirkes, så detaljeret en meddelelse som muligt
  - Oprette en bemærkning om for at give kunder med detaljerede nok oplysninger, så de kan udføre en undersøgelse på deres slutningen og opfylder forpligtelser over de har foretaget i deres slutbrugere mens ikke uberettiget forsinke processen.
  - Bekræft, og Definer hændelsen, som det er nødvendigt.
  - Besked om kunder med besked via hændelsen urimelige hurtigst muligt og i overensstemmelse med en hvilken som helst juridiske eller kontrakt anvendelsen. Meddelelse om sikkerhedshændelser vil blive leveret til en eller flere af en kundes administratorer på nogen måde, Microsoft markerer, herunder via mail.
- Forestå team parathed og undervisning
  - Microsoft personale er påkrævet for at afslutte sikkerhed og tilstedeværelse kursus, som hjælper dem med at identificere og rapportere mulige sikkerhedsproblemer.  
  - Operatorer, der arbejder på tjenesten Microsoft Azure har addition kursus forpligtelser over den omgivende deres adgang til følsomme systemer, der er vært for kundedata.
  - Microsoft sikkerhed svar personale modtage specialiserede kurser til deres roller


I tilfælde af tab af enhver kundedata underrette vi hver kunde i én dag. Dog er kunden datatab aldrig opstået med OMS. Desuden kan vi bevare kopier af data, der blev oprettet, og fordeles det geografisk.

Du kan finde flere oplysninger om, hvordan Microsoft reagerer på sikkerhed dem, [Microsoft Azure Security Response i skyen](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Overholdelse af angivne standarder

OMS software development og tjenesten gruppens oplysninger sikkerhed og styring program understøtter dens forretningsbehov og overholder lovgivning som beskrevet på [Microsoft Azure Sikkerhedscenter](https://azure.microsoft.com/support/trust-center/) og [Microsoft har tillid til Center overholdelse](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Hvordan OMS fastlægger sikkerhedskrav, identificerer sikkerhedskontrol, administrerer og overvåger risici beskrives også der. Årlig, vi foretager en gennemgang af politikker, standarder, procedurer og retningslinjer.

Hvert OMS udvikling gruppemedlem modtager formelle sikkerhedskursus. Vi bruger internt, et version control system for softwareudvikling. Hvert software projekt er beskyttet af version control system.

Microsoft har et sikkerhed og overholdelse team, der tager sig af og vurderer, hvor alle tjenester i Microsoft. Oplysninger om sikkerhed de udgør teamet, og de kan ikke knyttet til de tekniske afdelinger, udvikler OMS. De har deres egne ledelseskæde og forestå uafhængig vurdering af produkter og tjenester til at sikre sikkerhed og overholdelse af regler.

Microsofts bestyrelse er oplyst fra og årsrapport om alle sikkerheden for oplysninger programmer hos Microsoft.

OMS software development og tjenesten teamet arbejder aktivt med Microsofts Legal og overholdelse af angivne standarder teams og andre branchepartnere til at hente en lang række certificering.

## <a name="security-standards-certifications"></a>Sikkerhed i verdensklasse certificering

Log Analytics i OMS opfylder aktuelt følgende sikkerhed:

- [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) og [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) kompatibel
- Betaling kortet (PCI kompatibel) Data sikkerhed branchestandard (PCI DSS) af PCI sikkerhed standarder Rådet.
- [Tjenesten organisation kontrolelementer (SOC) 1 Type 1- og SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) kompatible
- Windows fælles teknisk kriterier
- Microsoft troværdig Computing-certificering
- Som en Azure service overholde de komponenter, der bruger OMS Azure overholdelseskrav. Du kan læse mere på [Microsoft har tillid til Center overholdelse](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).


## <a name="cloud-computing-security-data-flow"></a>Cloud computing sikkerhed dataflow
I det følgende diagram viser en sikkerhedsarkitekturen i skyen som strømmen af oplysninger fra din virksomhed, og hvordan det sikres, som er flytter til Log Analytics-tjenesten, i sidste ende fremgår af dig portalen OMS. Du kan finde flere oplysninger om hvert trin følger diagrammet.

![Billede af OMS dataindsamling og -sikkerhed](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. tilmelde sig til Log analyser og indsamling af data

For organisationen til at sende data til Log Analytics, kan du konfigurere Windows supportmedarbejdere, supportmedarbejdere, der kører på Azure virtuelle maskiner eller OMS supportmedarbejdere for Linux. Hvis du bruger Operations Manager supportmedarbejdere, skal du bruge en konfigurationsguiden i konsollen handlinger til at konfigurere dem. Brugere (som kan være dig, andre individuelle brugere eller en gruppe af personer) Opret en eller flere OMS konti (OMS arbejdsområder), og registrere supportmedarbejdere ved hjælp af en af følgende konti:


- [Organisationens ID](../active-directory/sign-up-organization.md)

- [Microsoft-konto – Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Et OMS arbejdsområde er, hvor data er der indsamles, sammenlagt, analysere og præsenteres. Et arbejdsområde, der primært bruges som et middel til partition data, og de enkelte arbejdsområder er entydige. Du kan for eksempel vil have dataene fremstilling administrerede med én OMS workspace og testdataene administreres med et andet arbejdsområde. Arbejdsområder Hjælp også en administrator kontrollere brugeradgang til dataene. De enkelte arbejdsområder kan have flere brugerkonti, der er knyttet til den, og hver brugerkonto kan få adgang til flere OMS arbejdsområde. Du kan oprette arbejdsområde, der er baseret på datacenter område. De enkelte arbejdsområder replikeres til andre datacentre i området, primært for OMS tjenesten kører.

Når konfigurationsguiden er fuldført, opretter hver Operations Manager management gruppe til Operations Manager, en forbindelse med Log Analytics-tjenesten. Du kan derefter bruge guiden Tilføj computere for at vælge, hvilke computere i gruppen management har tilladelse til at sende data til tjenesten. For andre typer af agent forbinder hver sikkert til OMS-tjenesten.

Al kommunikation mellem forbundne systemer og Log Analytics-tjenesten er krypteret.  TLS (HTTPS)-protokollen, der bruges til kryptering.  Microsoft SDL processen efterfølges for at sikre Log Analytics er opdaterede med de seneste fremskridt i cryptographic protokoller.

Hver type af agent indsamler data for Log analyser. Typen data, der er indsamlet er afhængig af typerne løsninger, der bruges. Du kan se en oversigt over dataindsamling på [tilføje Log Analytics solutions fra løsningsgalleriet](log-analytics-add-solutions.md). Desuden er mere detaljerede oplysninger om websteder tilgængelig for de fleste løsninger. En løsning er en masse foruddefinerede visninger, log søgeforespørgsler, regler for indsamling af data og behandlingslogik. Det er kun administratorer kan bruge Log Analytics for at importere en løsning. Når løsningen er importeret, flyttes den til Operations Manager management servere (hvis bruges), og klik derefter på en hvilken som helst supportmedarbejdere, du har valgt. Herefter indsamle på supportmedarbejdere dataene.

## <a name="2-send-data-from-agents"></a>2. sende data fra supportmedarbejdere

Du har registreret alle agent typer med en tilmelding nøgle og er oprettet en sikker forbindelse mellem agenten og Log Analytics-tjenesten ved hjælp af certifikat-baseret godkendelse og SSL med port 443. OMS anvender et hemmeligt lager til at oprette og vedligeholde taster. Private nøgler er roteret hver 90 dage og gemmes i Azure og administreres af handlingerne Azure som følger faste lovmæssige og overholdelse af regler fremgangsmåder.

Med Operations Manager, du har registreret et arbejdsområde med Log Analytics-tjenesten og er oprettet en sikker HTTPS-forbindelse mellem indholdsstyringsserver Operations Manager.

Til Windows-supportmedarbejdere, der kører på Azure virtuelle maskiner, er en skrivebeskyttet lagerplads nøgle bruges til at læse diagnosticering begivenheder i Azure tabeller.

Hvis en agent er i stand til at kommunikere med tjenesten for en eller anden grund, de indsamlede data er gemt lokalt i en midlertidig cache og management-serveren forsøger at sende dataene hver 8 minutter til to timer. Den agent cachelagrede data er beskyttet af det operativsystem til lagring af legitimationsoplysninger. Hvis tjenesten ikke kan behandle dataene efter 2 timer, kø supportmedarbejdere dataene. Hvis køen bliver fuld, starter OMS sluppet datatyper, startende med ydelsesdata. Agent kø grænsen er en nøgle i registreringsdatabasen, så du kan redigere den, hvis det er nødvendigt. Indsamlede data komprimeres og sendes til tjenesten, hvor du udelader lokale databaser, så den ikke tilføjer nogen belastning til dem. Når de indsamlede data er sendt, er den fjernet fra cachen.

Som beskrevet ovenfor, sendes data fra din supportmedarbejdere over SSL til Microsoft Azure datacentre. Du kan også kan du bruge ExpressRoute til at levere ekstra sikkerhed til dataene. ExpressRoute er en måde at oprette direkte forbindelse til Azure fra dit eksisterende WAN netværk, som en med flere protokol markere skifte (MPLS) VPN, der leveres af en udbyder af netværk. Du kan finde flere oplysninger [ExpressRoute](https://azure.microsoft.com/services/expressroute/).


## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Log Analytics-tjenesten modtager og behandler data

Tjenesten Log Analytics sikrer, at indgående data fra en pålidelig kilde ved at validere certifikater og dataintegritet med Azure-godkendelse. Ikke-behandlede rækkedata gemmes derefter som en blob i [Microsoft Azure-lager](../storage/storage-introduction.md) og krypteres ikke. Hver Azure lagerplads blob har dog et sæt af entydigt sæt af taster er kun tilgængelig for denne bruger. Typen data, der er gemt er afhængig af typerne løsninger, der blev importeret og bruges til at indsamle data. Derefter behandler tjenesten Log Analytics rækkedata for bloggen Azure-lager.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Brug Log Analytics til at få adgang til dataene

Du kan logge på Analytics Log på portalen OMS ved hjælp af organisationskonto eller en Microsoft-konto, du tidligere har oprettet. Al trafik mellem OMS portalen og Log Analytics i OMS sendes via en sikker HTTPS-kanal. Når du bruger portalen OMS, en session-ID genereres på bruger klienten (webbrowser) og data er gemt i en lokal cache, indtil sessionen afsluttes. Når afsluttes, slettes cachen. Klientsiden cookies, som ikke indeholder-personlige oplysninger, fjernes ikke automatisk. Cookies under sessionen er markeret HTTPOnly og er sikret. Efter et forudbestemt inaktiv periode afsluttes OMS portalen sessionen.

Ved hjælp af portalen OMS, kan du eksportere data til en CSV-fil, og du kan få adgang til data ved hjælp af søgning API'er. Csv-eksport er begrænset til 50.000 rækker per Eksportér og API data er begrænset til 5.000 rækker per søgning.

## <a name="next-steps"></a>Næste trin

- [Komme i gang med Log analyser](log-analytics-get-started.md) for at få mere at vide mere om Log analyser og få op at køre i minutter.
