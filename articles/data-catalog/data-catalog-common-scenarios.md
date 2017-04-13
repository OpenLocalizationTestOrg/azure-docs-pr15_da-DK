<properties
   pageTitle="Azure datakatalog almindelige scenarier | Microsoft Azure"
   description="En oversigt over almindelige scenarier, hvor Azure datakatalog, herunder registrering og registrering af værdiskabende-datakilder, aktivere sikrer business intelligence og hentning af eksisterende tribal viden om datakilder og processer."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/03/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-common-scenarios"></a>Azure datakatalog almindelige scenarier

I denne artikel viser almindelige scenarier, hvor Azure datakatalog kan hjælpe organisationer hente flere værdi fra deres eksisterende datakilder.

## <a name="scenario-1---registration-of-central-data-sources"></a>Scenarie #1 - registrering af central datakilder

Organisationer har ofte et antal værdiskabende datakilder. Disse datakilder medtage linje i business OLTP systemer, data lagre og business intelligence / analytics databaser. Antallet af systemer og overlap mellem systemer, vokser ofte over tid som business evolve behov, og som virksomheden selve udvikles via fusion og indkøb.

Det er ofte svært for brugerne at vide, hvor du vil placere dataene i disse datakilder. Er alle for almindelige spørgsmål som disse:

- Af de tre HR-systemer, der bruges i virksomheden, som skal jeg bruge til at oprette denne type rapport?
- Hvor skal jeg gå til Hent certificeret salgstal for regnskabsåret, der lige afsluttet?
- Hvem skal jeg stille, eller hvad er den proces, jeg skal bruge til at få adgang til datawarehouse?
- Jeg ved ikke, hvis tallene er højre – hvem der kan jeg bede om indsigt på hvordan disse data er meningen skal bruges, før jeg deler dette dashboard med mit team?

I dette scenarie, der kan hjælpe dig Azure datakatalog. De centrale, værdiskabende, IT-administrerede datakilder, der bruges på tværs af organisationen, er ofte logiske udgangspunktet for at udfylde kataloget. Selvom alle brugere kan registrere en datakilde, hjælper kataloget kick-started med de datakilder, der er mest sandsynlige at levere værdi til det største antal brugere drev tilpasning og brug af systemet. For kunder Introduktion til Azure datakatalog til kan at identificere og registrere de vigtigste datakilder, der bruges af mange forskellige grupper af dataforbrugere være det første trin til succes.

Dette scenarie giver også mulighed for at oprette anmærkninger værdiskabende datakilder for at gøre det lettere at forstå og få adgang til. Et vigtige aspekt af dette arbejde er skal indeholde oplysninger om, hvordan brugere kan anmode om adgang til datakilden. Azure datakatalog kan brugerne angive mailadressen på den bruger eller gruppe, der er ansvarlig for at styre kilde dataadgang, links til eksisterende værktøjer eller dokumentation eller gratis tekst, der beskriver processen, anmodning om adgang. Disse oplysninger, der er inkluderet i kataloget, brugere, der registrere registrerede datakilder, men som endnu ikke har tilladelse til at få adgang til dataene kan nemt anmode om adgang ved hjælp af processer, der er defineret og styres af datakilde ejere.

## <a name="scenario-2---self-service-business-intelligence"></a>Scenarie #2 – sikrer business intelligence

Selvom traditionelle virksomhedens business intelligence-løsninger fortsat vil være en uvurderlig del af mange organisationer data landskaber, ændre trit for virksomheder, der har skrevet selvbetjenings-BI mere og mere vigtige. Selvbetjenings-BI gør det muligt for it-medarbejdere og analytikere at oprette deres egne rapporter, projektmapper og dashboards uden at pege med en central IT-team – eller være begrænset af IT-teamet tidsplan og tilgængelighed.

I selvbetjenings-BI scenarier er det almindelige for brugerne at kombinere data fra flere kilder, hvoraf mange ikke muligvis har tidligere været brugt i BI og analyse. Selvom nogle af disse datakilder måske allerede kendt, er der ofte en proces for at se, hvad skal finde sted til at finde og evaluere potentielle datakilder for en bestemt opgave.

Traditionelt processen registrering er en manuel: analytikere anvender deres peer-netværksforbindelser til at identificere andre personer, du arbejder med de data, der søges om. Når en datakilde er fundet den kan bruges, men processen gentages, igen hver efterfølgende selvbetjenings-BI indsats, med flere brugere udføre den samme overflødige manuelle proces af registrering.

Brugere kan bryde denne cyklus af overflødige indsats med Azure datakatalog. Når en datakilde, der blev fundet på traditionelle måde, kan en analyse registrere datakilden for at gøre det mere nemmere i fremtiden. Selvom brugeren kan tilføje flere værdi ved at kommentere de registrerede dataaktiver, behøver det ikke finde sted på samme tid som registrering. Brugere kan bidrage over tid, som deres tidsplaner tilladelse til gradvist at øge værdien af de datakilder, der er registreret i kataloget.

Denne organiske vækst af kataloget indholdet er et neutralt supplement til forudgående registrering af central datakilder. Udfyldt udfylde kataloget med data, der skal mange brugere kan være en motivator til første brug og registrering. Aktivere brugere til at registrere og anmærke flere kilder, kan det være en metode til at holde dem og deres kolleger, ansat.

Det er også værd at bemærke, selvom dette scenarie fokuserer specifikt på selvbetjenings-BI, de samme mønstre og udfordringer gælder for store virksomhedens BI projekter samt. En hvilken som helst indsats, der involverer en manuel proces af kilde dataregistrering stammer fra en, der kan føje værdi til organisationen ved hjælp af Azure datakatalog.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Scenarie #3 – hente tribal knowledge

Hvordan ved du, hvilke data, skal du gøre tingene, og hvor du kan finde disse data?

Hvis du har været i dit arbejde til et stykke tid, ved du sandsynligvis lige. Du har gennemgået en gradvis læ proces og med tiden har lært om de datakilder, der er vigtige for dit daglige arbejde.

Når en ny medarbejder sammenkæder dit team, hvordan ved han så, hvilke data, skal han gør sin job, og hvor du kan finde det?

Sandsynligheden er, han beder dig om.

Denne igangværende overførsel af tribal viden er en del af processen datakilde registrering i organisationer store og små. Senior og erfarne teammedlemmer har opbygget knowledge for foregående år, og nyere teammedlemmer har lært at bede dem, når de har spørgsmål. De vigtigste oplysninger ofte findes kun i lederne af et par vigtige personer, og når personerne, der er på ferie eller forlader gruppen, organisationen langsommere.

Nogle gange disse data-eksperter, bliver den indsats til at dokumentere deres viden, dele den via mail eller i Word-dokumenter på et SharePoint-teamwebsted. Selvom det kan være værdifulde, det introducerer en ny registrering problem – hvordan personer ved hvilke dokumentation findes, og hvor du kan finde den...

Azure datakatalog her for at dele denne tribal viden, og til at gøre det nemmere. Data-eksperter direkte kan anmærke dataaktiver og kan også indeholde links til eksisterende dokumentation. Ikke kun betyder dette registrere viden selve, men også placeres viden i den samme oplevelse, der bruges til dataregistrering kilde. Når en person bruger kataloget til at finde en datakilde ikke kun er han finde kilden selve, han kan også finde den ekspert viden, som tidligere var kun i huske af eksperten sig selv.
