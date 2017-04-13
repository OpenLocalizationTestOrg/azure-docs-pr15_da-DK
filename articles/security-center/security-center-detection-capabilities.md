<properties
   pageTitle="Registrering af funktioner i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument hjælper dig med at forstå, hvordan Azure Sikkerhedscenter registrering funktioner fungerer."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Azure Sikkerhedscenter registrering af funktioner
Dette dokument omhandler Azure Security Center registrering af avancerede funktioner, som hjælper med at identificere aktive trusler målretning af dine Microsoft Azure-ressourcer og giver dig viden, der er behov for at svare hurtigt.

> [AZURE.NOTE] Avancerede registreringer er tilgængelige i Standard niveau af Azure Sikkerhedscenter. Der findes en gratis prøveversion 90 dage. Du kan opgradere fra [Sikkerhedspolitik](security-center-policies.md)priser niveau markeringen. Gå til [Sikkerhedscenter side](https://azure.microsoft.com/pricing/details/security-center/) for at få flere oplysninger om priser. 


## <a name="responding-to-todays-threats"></a>Besvare dags trusler
Der er sket væsentlige ændringer i truslen liggende over de sidste 20 år. I tidligere skulle firmaer typisk kun bekymre dig om webstedet defacement ved individuelle hackere, som hovedsageligt er interesseret i at se "hvad brugeren kan gøre". Dags hackere er meget mere avancerede og organiseret. De har ofte bestemte finansielle og strategiske mål. De har også flere ressourcer, der er tilgængelige i dem, som de kan finansieres af hele landet tilstande eller organiseret kriminalitet.

Denne metode har ført til en hidtil ukendt niveau mere professionelt udseende i hacker rangordner. Ikke længere er de interesseret i web defacement. De nu er interesseret i at stjæle oplysninger, regnskaber og private data – som alle de kan bruge, til at generere kontant på markedet Åbn eller udnytte en bestemt business, politiske eller militære placering. Endnu mere vedrørende end disse hackere med en økonomisk målsætning er de hackere, som bryde netværk til at skade til infrastruktur og personer.

Svar implementere organisationer ofte forskellige punkt løsninger, der fokuserer på forsvare enten enterprise afskærmede eller slutpunkterne ved at søge efter kendte angreb signaturer. Disse løsninger har tendens til at oprette en stor mængde lav pålidelighed beskeder, der kræver en sikkerhed analyse til at prioritere og undersøge. De fleste organisationer mangler tid og ekspertise til at reagere på disse beskeder om – så mange gå uadresserede.  I mellemtiden har hackere udviklet deres metoder til at subvert mange signatur-baserede beskyttelse og [tilpasse til skyen miljøer](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nye metoder er påkrævet for at identificere trusler og fremskynde registrering og svar hurtigere. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Hvordan Azure Sikkerhedscenter registrerer og reagerer på trusler

Forskere i Microsoft sikkerhed der konstant opmærksom på, om trusler. De har adgang til et stort antal telemetri erfaringer fra Microsofts global tilstedeværelse i skyen og i det lokale miljø. Denne at kontakte wide og forskelligartet samling af datasæt hjælper Microsoft med at opdage nye angreb mønstre og tendenser på tværs af dens lokale forbruger- og enterprise-produkter samt virksomhedens onlinetjenester. Sikkerhedscenter kan som et resultat hurtigt opdatere dens registrering algoritmer, som hackere slip tilsidesættelse af nye og mere og mere avancerede. Denne fremgangsmåde hjælper dig med at holde trit med et hurtigt flytte truslen-miljø. 

Sikkerhedscenter truslen registrering fungerer ved at indsamle automatisk sikkerhedsoplysninger fra ressourcerne Azure, netværk og forbundne partnerløsninger. Det analyserer disse oplysninger, der ofte korrelere oplysninger fra flere kilder til at identificere trusler. Sikkerhedsadvarsler er prioriteret i Sikkerhedscenter sammen med anbefalinger om, hvordan du afhjælpning truslen.

![Sikkerhedscenter dataindsamling og -præsentation](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Sikkerhedscenter anvender Avanceret sikkerhed analytics, hvor langt overgår signatur-baserede fremgangsmåder. Gennembrud i big data og [maskine læ](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) teknologier opgraderede for at evaluere begivenheder på tværs af hele skyen strukturen – registrering af trusler, der ville være umuligt at identificere ved hjælp af manuel fremgangsmåder og forudsigelser udviklingen af angreb. Disse sikkerhed analytics omfatter: 

- **Integreret truslen intelligence**: søger efter kendte dårlige aktører ved at udnytte globale truslen intelligence fra Microsoft-produkter og tjenester, Microsoft Digital forbrydelser enhed (DCU), Microsoft sikkerhed svar Center MSRC () og eksterne kilder.
- **Funktionsmæssige analytics**: gælder kendte mønstre for at se skadelig opførsel. 
- **Anomali registrering**: bruger statistiske profiler til at opbygge en historiske oprindelig plan. Det giver besked på afvigelse fra eksisterende grundlinjer, der er i overensstemmelse med en mulig angreb.


### <a name="threat-intelligence"></a>Truslen intelligence
Microsoft har en store mængde globale truslen intelligence. Telemetri løber i fra flere kilder, såsom Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital forbrydelser enhed (DCU) og Microsoft sikkerhed svar Center MSRC (). Forskere modtager også truslen intelligence oplysninger, der deles af overordnede skyen tjenesteudbydere og abonnerer på truslen intelligence feeds fra tredjeparter. Azure Sikkerhedscenter kan bruge disse oplysninger til at advare dig til trusler fra kendte dårlige aktører. Nogle eksempler kan nævnes:

- **Udgående kommunikation til en skadelig IP-adresse**: udgående trafik til en kendte botnet eller darknet sandsynligvis angiver, at din ressource er blevet afsløret og en hacker det forsøg på at udføre kommandoerne under system eller exfiltrate dataene. Azure Sikkerhedscenter sammenligner netværkstrafik til Microsofts globale truslen database og giver dig besked, hvis der registreres kommunikation til en skadelig IP-adresse.

## <a name="behavioral-analytics"></a>Funktionsmæssige analytics

Funktionsmæssige analytics er en teknik, der analyserer og sammenligner data til en samling af kendte mønstre. Disse mønstre er dog ikke enkle signaturer. De bestemmes gennem komplekse machine learning-algoritmer, der gælder for store datasæt. De bestemmes også via forsigtig analyse af skadelig funktionsmåder af expert analytikere. Azure Sikkerhedscenter kan bruge funktionsmæssige analytics til at identificere kompromitteret ressourcer, der er baseret på analyse af virtuelt logfiler, virtuelt netværk enhed logfiler, strukturen logfiler, Crashdumps og andre kilder. 

Desuden er der korrelations med andre signaler for at kontrollere, om understøttende dokumentation for en omfattende campaign. Denne korrelations hjælper med at identificere hændelser, der er i overensstemmelse med eksisterende indikatorer af skade. Nogle eksempler kan nævnes:

- **Suspicious behandle udførelse af**: hackere anvender nogle teknikker til at udføre skadelig software uden registrering. For eksempel kan en hacker give malware de samme navne som gyldige systemfiler, men placere disse filer på en anden placering, give den et navn, som minder meget om en nyttigt fil eller maske filens SAND filtypenavn. Sikkerhedscenter modeller processer opførsel og skærme behandle udførelser at registrere outliers som disse.  
- **Skjulte malware og udnyttelse forsøger**: avancerede malware er kunne blive traditionelle Antimalwareprogram produkter efter aldrig skrivning til disk eller kryptere softwarekomponenter, der er gemt på disken.  Dog kan sådanne malware findes ved hjælp af analyse af hukommelse, som malware skal lade sporinger i hukommelsen for at kunne fungere. Når software går ned, registrerer en crashdump en del af hukommelse på tidspunktet for et nedbrud.  Ved at analysere hukommelse i nedbrud dumpet kan kan Azure Sikkerhedscenter registrere teknikker, der bruges til at udnytte svagheder i softwaren, få adgang til fortrolige data og al hemmelighed fastholdes med-i en kompromitteret maskine uden at påvirke ydeevnen for din computer.
- **Tværgående bevægelse og interne reconnaissance**: kan du bevare i et kompromitteret Netværks- og finde/høst værdifulde data, hackere ofte forsøg på at flytte lateralt fra kompromitteret maskine til andre i det samme netværk. Sikkerhedscenter overvåger proces og logge aktiviteter for at finde forsøg på at udvide en hacker foothold i netværk, som eksterne kommandoen udførelse af netværk test, og konto optælling.
- **Skadelige PowerShell-Scripts**: PowerShell bruges af hackere udføre skadelig kode på target virtuelle maskiner til en række forskellige formål. Sikkerhedscenter undersøger PowerShell aktivitet for tegn på mistænkelig aktivitet. 
- **Udgående angreb**: hackere ofte målrette skyen ressourcer med formålet med brug af disse ressourcer til at tilslutte yderligere angreb. Kompromitteret virtuelle maskiner, kan for eksempel bruges til at starte råstyrke angreb på andre virtuelle maskiner, sende SPAM eller scanne porte og andre enheder på internettet. Sikkerhedscenter kan registrere ved at anvende machine learning til netværkstrafik, når udgående netværk kommunikation overskrider normen. Hvis det er SPAM, Sikkerhedscenter også svarer usædvanlige mail trafik med intelligence fra Office 365 for at afgøre, om e-mailen er sandsynligvis nefarious eller resultatet af en ægte mail campaign.  

### <a name="anomaly-detection"></a>Anomali registrering

Azure Sikkerhedscenter bruges også anomali registrering til at identificere trusler. I modsætning til funktionsmæssige analytics (som afhænger af kendte mønstre, der er afledt af store datasæt), anomali registrering er mere "tilpasset" og fokuserer på oprindelige planer, der er specifikke for din installationer. Machine learning anvendes for at afgøre normal aktivitet for dine installationer, og klik derefter regler der oprettes for at definere udenforliggende betingelser, der kan repræsentere en sikkerhed begivenhed. Her er et eksempel:

- **Indgående RDP/SSH brute gennemtvinge angreb**: din har installationer optaget virtuelle maskiner med en masse logon hver dag og andre virtuelle maskiner, der har meget få eller en hvilken som helst logon. Azure Sikkerhedscenter kan fastlægge oprindelige login aktivitet for disse virtuelle maskiner og bruge machine learning til at definere, hvad er uden for normal login aktivitet. Hvis antallet af logon, eller -klokkeslættet for dagen i logon, eller den placering, hvor der anmodes om logon eller andre logon-relaterede egenskaber er signifikant forskellige fra den oprindelige plan, kan derefter en besked genereres. Machine learning bestemmer igen, hvad er betydeligt.

## <a name="continuous-threat-intelligence-monitoring"></a>Kontinuert truslen intelligence overvågning

Azure Sikkerhedscenter fungerer sikkerhed forskning og data videnskabelige teams, der løbende overvåger til ændringer i truslen liggende. Dette omfatter følgende initiativer:

- **Truslen intelligence overvågning**: truslen intelligence omfatter mekanismer, indikatorer, konsekvenserne og handlings rådgivning om eksisterende eller nye trusler. Disse oplysninger deles i gruppen sikkerhed og Microsoft overvåger løbende truslen intelligence feeds fra interne og eksterne kilder.
- **Deling af signal**: indsigt fra sikkerhed grupper på tværs af Microsofts bredt udvalg af skyen og lokale tjenester, servere og klienten slutpunkt enheder delt og analysere. 
- **Microsoft sikkerhed specialister**: igangværende aftale med grupper på tværs af Microsoft, der fungerer i specialiserede sikkerhed felter, som forensics og web angreb registrering.
- **Justering af registrering**: algoritmer som køres mod kunders datasæt og forskere i sikkerhed arbejde med kunder til at validere resultaterne. SAND og falsk positive bruges til at afgrænse machine learning algoritmer.

Disse kombinerede bestræbelser kunne få nye og forbedrede registreringer, som du kan få glæde af med det samme – der findes ingen handling for dig at tage.

## <a name="see-also"></a>Se også
I dette dokument, du har lært hvordan til Azure Sikkerhedscenter registrering funktioner fungerer. Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Azure Sikkerhedscenter planlægning og Operations Guide](security-center-planning-and-operations-guide.md)
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md)
- [Sikkerhedsadvarsler efter Type i Azure Security Center](security-center-alerts-type.md)
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Overvågning partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – finde blogindlæg om Azure sikkerhed og overholdelse af regler.
