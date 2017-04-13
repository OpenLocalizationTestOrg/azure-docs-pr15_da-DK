<properties
   pageTitle="Azure SQL Database Azure Casestudie - GEP | Microsoft Azure"
   description="Få mere at vide om, hvordan GEP anvender SQL-Database til at nå mere globale kunder og opnå større effektivitet"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure giver GEP global rækkevidde og større effektivitet

![GEP-Logo](./media/sql-database-implementation-gep/geplogo.png)

GEP leverer software og -tjenester, der aktiverer indkøb fyldtegn over hele verden at maksimere deres indvirkning på deres forretninger operationer, strategier og finansielle ydeevne. Ud over rådgivning og administrerede services tilbyder virksomheden SMART ved GEP®, en skybaseret, omfattende indkøb-software platform. Dog GEP opstod begrænsninger forsøg på at understøtte løsninger, såsom SMART ved GEP med sin egen lokale datacentre: det er nødvendigt investeringer blev stejle og lovmæssige krav i udlandet vil har foretaget de nødvendige investeringer mere overvældende stadig. Ved at flytte til skyen, har GEP frigjort IT-ressourcer, give tilladelse til at bekymre dig mindre om IT-aktiviteter og til at fokusere mere om at udvikle nye kilder af værdi for kunderne over hele verden.

## <a name="expanding-services-and-growth-by-using-azure"></a>Udvide tjenester og forøgelse ved hjælp af Azure

SMART ved GEP kunder elsker platformen funktioner og nemmere at bruge; kunder kan administrere deres processer fra et vilkårligt sted, når som helst og på en hvilken som helst enhed – bærbare computer, tablet eller telefon. Ved at flytte til Microsoft Azure, har GEP været stand til at skabe plads til dens hurtig vækst og dens mulighed for at udvide til nye markeder. Ifølge GEPS Underdirektør af teknologi, Dhananjay Nagalkar er "Microsoft Azure afspillet en vigtig rolle i GEPS succes ved at give os til at skalere hurtigt services med fleksibilitet og ved at indsende internationale datacentre, hjælper os med at imødekomme lovmæssige kunderne global."

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>Begrænsninger for en gør det selv datacenter

I 2013 genkendte GEP fyldtegn, at de skal bruges en metode til at sikre skalerbarhed og ydeevne, som de gamle deres kunden basis. Nagalkar forklaring, "for at opfylde denne efterspørgsel med vores eksisterende datacentre, vi ville have haft at udvide vores infrastruktur og IT-ressourcer betydeligt. Investering og tidsrammen for, der ville have været optager enormt meget plads." Lokale fysiske og virtuelle maskiner kræver omfattende konfiguration, administration, skalering, sikkerhedskopiering og retter med en hastighed, der ville have været forbud til GEP omkostninger. Skyen løsninger på den anden side tilbyder enkle og nemmere, aktiveres GEP at fokusere på mere på udvikling i stedet for administration af store – og voksende – IT-aktiviteter. Nagalkar havde kendskab, GEP kan reducere dens infrastruktur købe, konfiguration og administration af omkostninger ved at skifte til skyen.

GEP behov også en måde at løse lovmæssige hindringer, der bevares af nogle globale markeder. For mange af GEPS potentielle europæiske kunder kræver overholdelse af lovgivningen har data gemt i deres lokale geografiske områder. Men det ikke ville have været praktisk, hvis GEP udvidelse flere datacentre. "Udbredte infrastrukturinvesteringer og IT arbejde omkostninger vil flytte en betydeligt virkning til margener," efter Nagalkar. "Som et resultat, vi blev faktisk nødt til at slå ikke til stede potentielle kunder, der kræves lokalt gemte data."

Nagalkar vidste, at en skybaseret løsning måske svaret på dette dilemma. Hvis GEP kunne flyttes til en udbyder af skyen med global tilstedeværelse, kan det bedre opfylder kundernes lovmæssige og ydeevne skal ved at gemme data tættere til kundernes fysiske placeringer.

## <a name="finding-smooth-skies-in-the-cloud"></a>Finde udglattede skies i skyen

Nagalkar og hans team set flere indstillinger i skyen, men de fleste blev infrastruktur som-en-tjenesten (IaaS)-baserede løsninger, der ville have påkrævet GEP at investere stærkt i IT-ressourcer til at konfigurere og administrere tjenesten. Azure platform som en tjeneste (PaaS) løsningen slået ud af, skal være en meget bedre passer.

Nagalkar angivet "Med Azure, GEP ikke brug for at håndtere database management, virtuelt konfiguration, retter eller andre infrastruktur administrationsopgaver". "I stedet vi kan fokusere vores ressourcer på hvad vi gør bedst: udnytte vores ekspertise i indkøb at skrive software, der giver virkelig resultater for vores kunder." 

Flyt til Azure har faktisk aktiveret GEP formindske personalet IT handlinger under aktivering samtidigt større funktionalitet for kunder.

Ved at drage fordel af Azure datacentre over hele verden, udvide GEP nemt dets rækkevidde på tværs af Europa og Asien. Disse globale datacentre aktivere GEP skalere med fleksibilitet og til at opfylde kundernes behov for lokalt gemte data, der reducerer ventetid og opfylder lovmæssige krav.

## <a name="smart-by-gep-architecture"></a>SMART ved GEP arkitektur

GEP opbygget SMART ved GEP fra bunden på Azure. En kritiske motivation for GEP blev større skalerbarhed, mindre nedetid, og begrænsede vedligeholdelsesomkostninger, GEP kan opleve med Azure SQL-Database sammenlignet med hvilke GEP kan opnå i det lokale miljø. Når flyttet til skyen, opdaget GEP dog nye udviklingsmuligheder for i skyen, som hurtig prototyper og slank engineering bedre reagere på kundernes behov. Udvikling i Azure lade GEP gøre ikke til stede med softwarelicensen hovedbrud, dens udviklere kan støde på i det lokale miljø. Kernen i SMART ved GEP er Azure SQL-Database, selvom GEP bruger mange andre Azure tjenester kan nemt og hurtigt fortsætte med at forbedre SMART ved GEP.

![SMART ved GEP arkitektur](./media/sql-database-implementation-gep/figure1.png) figur 1. SMART ved GEP arkitektur

## <a name="structured-data"></a>Strukturerede data

Essensen af SMART af GEP program er Azure SQL-Database forekomster pågældende power enterprise indkøb-management-løsning. Når GEP engineering SMART ved GEP, så Azure SQL-Database som en perfekt for dets arkitektur, en, der ville gøre det muligt for firmaet til at opnå den største mulige databeskyttelse og med lovmæssige krav. GEP gør brug af flere lag til beskyttelse af data, som Azure SQL-Database indeholder, herunder:

- Kryptering af data på resten gennem gennemsigtig datakryptering.
- Sikring af godkendelse ved at integrere Azure SQL-Database med Azure Active Directory.
- Begrænse adgang til en passende delmængde af data med sikkerhed på brugerniveau række.
- Kombinere data i realtid via politikker.
- Sporing af database begivenheder via Azure SQL Database overvågning.

> "Vi kan bruge alle disse indstillinger uden at foretage kodeændringer overordnede og med minimale påvirkning af ydeevnen," sagde Nagalkar.

Ved hjælp af Azure SQL-Database, har GEP automatisk større nedbrud funktioner, end det kunne har økonomisk engineering lokalt på grund af funktionerne fejltolerance for indbygget i Azure SQL-Database. GEP bruger muligheden for aktive geografisk-gentagelse i Azure SQL-Database, forbundet med flere aktivt, kan læses og online sekundær replikaer (altid på tilgængelighed grupper) i forskellige geografiske områder, til at danne par høj tilgængelighed. Replikering SMART ved GEP betyder data på tværs af områder, at i tilfælde af nedbrud område hele, GEP kan nemt gendanne kundedata med et minimum gendannelse punkt mål (frigivne Produktionsordre) og gendannelse tid mål (RTO).

Hver SMART af GEP kunden har to Azure SQL-Database forekomster: én for behandling af online transaktioner (OLTP) og én til analyse (såsom kunde bruger og rapportere analyse). Azure SQL-Database elastiske database grupper aktivere GEP til at styre tusindvis af databaser globalt til at håndtere uventede database er ressourcerelaterede krav. Elastiske grupper giver mulighed for GEP til at sikre, at kunde databaser kan skalere det er nødvendigt, uden at uberettiget klargøringen eller under-klargøring, mens du også tillade GEP til at styre omkostningerne. Da dette er en PaaS-tjeneste, bliver GEP desuden alle de nye Azure SQL-Database funktioner med automatiske opgraderinger.

## <a name="unstructured-and-semi-structured-data"></a>Ustrukturerede og semistrukturerede data

Nogle SMART ved GEP kundedata skal dog mindre stift strukturerede lagerplads. For denne type data anvender GEP Azure Blob-lager, Azure Table Storage og Azure Redis Cache. Azure Blob-lager indeholder vedhæftede filer, der SMART ved GEP brugere Overfør til programmet. Det er også hvor SMART ved statisk indhold til GEP butikker, som overlappende typografiark (CSS) og JavaScript-filer.

GEP gemmer uden kunde-adgang til data, som SMART ved GEP logdata i Azure Table Storage, hvilket giver GEP effektivt ubegrænset økonomisk lager og hurtigt at hente gange uden at skulle bekymre dig om at konfigurere et skema for dataene. GEP bruger Azure Redis Cache for en masterside cache.

## <a name="authentication-and-routing"></a>Godkendelse og routing

Azure Access ACS (Control Service) indeholder SMART af GEP brugere med en lang række muligheder for at logge ind i softwaren. Azure ACS kan oprette til en identitetsudbyder, samtaler, godkendelsesdata ved hjælp af sikkerhed program Markup Language (SAML), som Active Directory-domænetjenester, Ping identitet, OneLogin eller SiteMinder. Det hjælper med GEP implementere enkeltlogon (SSO) til kunder uden at bekymre dig om lagring brugerlegitimationsoplysninger og vedligeholde kunde adgangskodepolitikker.

Når du er logget, har kunder adgang til de korrekte business-ressourcer i SMART ved GEP. GEP bruger Azure trafik Manager til at omdirigere og belastning anmodninger kommer fra kundernes mobilenheder og browsersessioner.

## <a name="other-azure-services"></a>Andre Azure tjenester

GEP anvender et antal andre Azure tjenester for at sikre SMART ved GEP svarede til kunde skal. GEP bruger Azure skytjenester (både internettet og arbejder roller) til at host program-præsentation og sikkert forretningslogik tjenesterne. Skytjenester gør det muligt for udviklere til at administrere infrastruktur som kode (IAC) og installere nye SMART af GEP programmer på en brøkdel af den tid, det bruges med lokale datacentre – alt sammen uden en hvilken som helst deltagelse fra IT. GEP udviklere kan bruge skytjenesterne testmiljø for at teste nye versioner uden at påvirke den aktuelle produktionsmiljø. Når testet, bruger GEP VIP Byt om funktionerne i Azure skytjenester til at flytte den midlertidige kode til fremstilling plads i et minut, hvilket reducerer installation nedetid.

Hvis du vil sænke programmet ventetid, bruger GEP Azure indhold levering netværk (CDN) til at placere statisk indhold, der er gemt i Azure Blob-lager (såsom CSS og JavaScript-filer) på edge-servere tæt brugere. GEP anvender Azure Service Bus til at understøtte program-arkitektur mønstre fra udgivelse-abonnement til delvist kommandoen forespørgsel svarede opdeling (CQRS) og lagt arkitektur med Løs kobling og asynkron kommunikation. GEP bruger Azure Media Services til at forbedre dens kundesupport tjeneste. GEP fundet, kan det let udgive brugersupport videoer på Azure Media Services. Disse videoer besvare almindelige brugerspørgsmål, som hjælper med at forbedre SMART ved GEP brugertilfredsheden mens du tager nogle af support Indlæs fra GEPS kundesupport personale.

Hvis du vil sende tusindvis af transaktions mails genereret dagligt SMART ved GEP, bruger virksomheden SendGrid .NET API til at integrere med Azure. For udviklere GEP, er det nemt at gøre – SendGrid tilføjelsesprogrammet til Azure er tilgængelige direkte i Azure Marketplace. GEP udviklere kan konfigurere SMART ved GEP ved hjælp af SendGrid NuGet pakke højre i Microsoft Visual Studio GEP IT kan overvåge den software SendGrid mail trafik direkte fra Azure.

Til sidst skal SMART ved GEP bruger virtuelle Azure-computere – tjenesten Azure IaaS – til host programmer og tjenester, der ikke giver mening, samtidig med ingeniørarbejde, hvis du vil erstatte med software som-en-tjenesten (SaaS) eller PaaS løsninger. For eksempel vært GEP integration API tjenester i virtuelle maskiner til business to business (B2B) integration med kundernes lokale enterprise resource planning (ERP) systemer som SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics generel og Lawson og med kunde SaaS løsninger til at udveksle effektivt indkøb dokumenter, som fakturaer.

> "SMART opbygning af GEP i Microsoft Azure skyen er helt fjernet behovet for lokale IT, ikke kun for GEP men også for vores kunder indkøb operationer." 

> – Dhananjay Nagalkar, Vicedirektør for teknologiske løsninger

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Udvid kundetilfredshed uden at udvide IT

Siden overførsel fra lokale datacentre til Azure, og udvikling af SMART ved GEP fra bunden på Azure-platformen, er GEP forøget skalerbarhed og fleksibilitet uden at udvide dets infrastruktur eller IT-medarbejdere. Faktisk føjet virksomheden ikke IT-ressourcer i mere end fire år. Praktisk PaaS modellen af Azure har aktiveret GEP at reducere den bruger på Administration af leverandører support og handlinger. Som et resultat er GEP blevet kan fokus ressourcer på softwareudvikling; og udvikling i skyen gør det muligt for GEP udviklere til hurtigt at afprøve nye ideer uden at bruge en masse tid koordinere med IT eller at bekymre dig om lokale licenskravene. Azure SQL-Database kan GEP bedre sikre, at kunderne altid har særlige tjeneste og ydeevne.
 
## <a name="more-information"></a>Få mere at vide

- Startsiden for GEP: [GEP](http://www.gep.com)
- Smart ved GEP: [Smart ved GEP](http://www.smartbygep.com)

##<a name="gep-contributors"></a>GEP bidragydere

- Huzaifa Matawala knytte direktør – Architect, GEP
- Sathyan Narasingh, teknisk Manager GEP
- Deepa Velukutty, Database Architect GEP
