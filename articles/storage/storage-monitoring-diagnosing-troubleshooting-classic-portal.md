<properties
    pageTitle="Overvåge, diagnosticere og foretage fejlfinding af lagerplads | Microsoft Azure"
    description="Du kan bruge funktioner som f.eks lagerplads analytics, logføring på klientsiden og andre værktøjer fra tredjepart til at identificere, diagnosticere og foretage fejlfinding af Azure-lager-relaterede problemer."
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Overvåge, diagnosticere og fejlfinding i forbindelse med Microsoft Azure-lager

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Oversigt

Diagnosticere og fejlfinde problemer i et distribueret program, der er placeret i et skybaseret miljø kan være mere kompleks end i traditionelle miljøer. Programmer kan installeres i en PaaS eller IaaS infrastruktur på en mobilenhed eller i en kombination af disse lokalt. Typisk programmets netværkstrafik kan gennemgang offentlige og private netværk og dit program kan bruge flere storage-teknologier som Microsoft Azure-lagertabeller, blob, køer eller filer ud over andre data lagrer sådanne som relationelle og -databaser.

Hvis du vil administrere sådanne programmer korrekt skal du overvåge dem proaktiv og forstå, hvordan du diagnosticere og fejlfinding i forbindelse med alle aspekter af dem og deres afhængige teknologier. Som en bruger af Azure-lager tjenester, skal du løbende overvåge lagerplads tjenester dit program bruger til uventede ændringer i funktionsmåde (såsom langsommere end normalt svar gange) og bruge logføring til at indsamle mere detaljerede data og til at analysere et problem i dybden. Diagnosticeringsoplysninger, får du fra både overvågning og registrering hjælper dig med at finde ud af den egentlige årsag dit program er stødt på problemet. Derefter kan du foretage fejlfinding af problemet og finde de relevante trin, du kan tage for afhjælpning det. Azure-lager er en central Azure service, og danner en vigtig del af fleste løsninger, der installeres kunder til Azure infrastrukturen. Azure-lager indeholder funktioner til at forenkle overvågning, diagnosticering og fejlfinding af problemer med lager i din skybaserede programmer.

> [AZURE.NOTE] Lagerplads konti med en gentagelse af typen af Zone overflødige lagerplads (ZRS) har ikke målepunkter eller muligheden for logføring aktiveret på nuværende tidspunkt. 

Du kan finde en praktisk vejledning til at foretage fejlfinding af til slut i Azure-lager programmer [Til en komplet fejlfinding ved hjælp af Azure Lagringsmål og logføring, AzCopy, og meddelelsen Analyzer](storage-e2e-troubleshooting.md).

+ [Introduktion]
    + [Hvordan denne vejledning er organiseret]
+ [Overvågning af dine lagringstjeneste]
    + [Overvågning af tjenestetilstand]
    + [Overvågning kapacitet]
    + [Overvågning tilgængelighed]
    + [Overvågning af ydeevnen]
+ [Diagnosticere lagerplads problemer]
    + [Service sundhed problemer]
    + [Problemer med ydeevnen]
    + [Diagnosticere fejl]
    + [Lagerplads emulator problemer]
    + [Lagerplads logføring værktøjer]
    + [Ved hjælp af netværk logføring værktøjer]
+ [Sporing af til slut]
    + [Korrelerende logdata]
    + [Klient anmodnings-ID]
    + [Server anmodnings-ID]
    + [Tidsstempler]
+ [Vejledning til fejlfinding]
    + [Målepunkter vise høj AverageE2ELatency og lav AverageServerLatency]
    + [Målepunkter vise lav AverageE2ELatency og lav AverageServerLatency, men klienten oplever lang ventetid]
    + [Målepunkter Vis høj AverageServerLatency]
    + [Du oplever uventede forsinkelser i levering af meddelelser på en kø]
    + [Målepunkter vise en stigning i PercentThrottlingError]
    + [Målepunkter vise en stigning i PercentTimeoutError]
    + [Målepunkter vise en stigning i PercentNetworkError]
    + [Klienten modtager HTTP 403 (forbudt) meddelelser]
    + [Klienten modtager HTTP 404 (blev ikke fundet) meddelelser]
    + [Klienten modtager HTTP 409 (konflikt) meddelelser]
    + [Målepunkter vise lav PercentSuccess eller analytics logposter have handlinger med transaktionsstatus af ClientOtherErrors]
    + [Kapacitet målepunkter vise en uventede stigning i kapacitet lagerforbrug]
    + [Du oplever uventede genstarter af virtuelle maskiner, der har et stort antal vedhæftede virtuelle harddiske]
    + [Dit problem opstår i at bruge lagerplads emulatoren til udvikling eller test]
    + [Du oplever problemer med at installere Azure SDK til .NET]
    + [Du har et andet problem med en lagringstjeneste]
+ [Tillæg]
    + [Tillæg 1: Brug af Fiddler til at registrere HTTP og HTTPS trafik]
    + [Tillæg 2: Brug af Wireshark til at registrere netværkstrafik]
    + [Tillæg 3: Brug af Microsoft meddelelse Analyzer til at registrere netværkstrafik]
    + [Tillæg 4: Brug af Excel til at få vist målepunkter og logge af data]
    + [Tillæg 5: Overvågning med programmet indsigt til Visual Studio Team Services]

## <a name="introduction"></a>Introduktion

Denne vejledning viser, hvordan du bruger funktioner som Azure lagerplads Analytics relaterede klientsiden logføring i biblioteket Azure lagerplads klienten og andre værktøjer fra tredjepart til at identificere, diagnosticere og foretage fejlfinding af Azure-lager problemer.

![][1]

*Figur 1 overvågning, diagnosticering og fejlfinding*

Denne vejledning er beregnet til at blive læst først og fremmest af udviklere af onlinetjenester, der bruger Azure lagerplads Services og IT-medarbejdere, der er ansvarlig for administration af disse onlinetjenester. Denne vejledning mål er:

- Kan hjælpe dig med at vedligeholde tilstand og ydeevnen af kontiene Azure-lager.
- Til at give dig de nødvendige processer og værktøjer til at afgøre, hvis en problem eller et problem i et program relaterer til Azure-lager.
- Til at give dig handlings vejledning til løsning af problemer i forbindelse med Azure-lager.

### <a name="how-this-guide-is-organized"></a>Hvordan denne vejledning er organiseret

Afsnittet "[overvågning din lagringstjeneste]" beskriver, hvordan til at overvåge systemtilstand og ydeevnen af din Azure-lager-tjenester med Azure Analytics Lagringsmål (Lagringsmål).

Afsnittet "[diagnosticere lagerplads problemer]" beskriver, hvordan diagnosticere problemer ved hjælp af Azure lagerplads Analytics logføring (lagerplads logføring). Sådan aktiveres logføring på klientsiden ved hjælp af funktionerne og derefter på et af klient-bibliotekerne som biblioteket lagerplads klient til .NET eller Azure SDK til Java beskrives også.

Afsnittet "[sporing fra slutpunkt til]" beskrives, hvordan du kan justere oplysningerne i forskellige logfiler og målepunkter.

Afsnittet "[fejlfinding vejledning]" indeholder fejlfinding vejledning til nogle af de almindelige lagerplads-relaterede problemer kan du støde på.

"[Tillæg]" indeholder oplysninger om brug af andre værktøjer som Wireshark og Netmon til at analysere pakke netværksdata, Fiddler til at analysere HTTP/HTTPS meddelelser og Microsoft meddelelse Analyzer til korrelere logdata.


## <a name="monitoring-your-storage-service"></a>Overvågning af dine lagringstjeneste

Hvis du er bekendt med Windows overvågning, kan du betragte Lagringsmål som værende en Azure-lager udgave af Windows Performance Monitor tællere. Du kan finde et omfattende sæt af målepunkter (tællere i Windows Performance Monitor terminologi) i Lagringsmål som tjenesten kører, samlede antal forespørgsler til webtjenesten eller procentdel af vellykkede forespørgsler til tjenesten (en komplet liste over tilgængelige målepunkter, se for <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Lagerplads Analytics målepunkter tabelskema</a> på MSDN). Du kan angive, om du vil tjenesten storage til at samle og sammenlægge målepunkter hver time eller hvert minut. Du kan finde flere oplysninger om, hvordan du aktiverer målepunkter og overvåge kontiene lagerplads, kan du se <a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">aktivere lagringsmål</a> på MSDN.

Du kan vælge, hvilke hver time målepunkter du vil have vist i portalen Azure klassisk og konfigurere regler, der besked administratorer via mail, når en hver time metrisk overskrider en bestemt grænse (du kan finde flere oplysninger, får du vist siden <a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">Sådan: modtage påmindelser og administrere regler for påmindelser i Azure</a>). Tjenesten storage indsamler ved hjælp af en bedste indsats, men kan ikke angive hver handling.

Figur 2 nedenfor viser siden skærm på klassisk Azure-portalen, hvor du kan få vist målepunkter som tilgængelighed, samlet antal anmodninger og forsinkelse på gennemsnitligt tal for en lagerplads konto. En meddelelse om regel har også angive at give besked om en administrator, hvis tilgængelighed falder under et bestemt niveau. I at få vist disse data, ét mulige område til undersøgelsen er tabel service succes der under 100% (du kan finde flere oplysninger i afsnittet "[målepunkter vise lav PercentSuccess eller analytics logposter have handlinger med transaktion status for ClientOtherErrors]").

![][2]

*Figur 2 visning lagringsmål i portalen Azure klassisk*

Du skal løbende overvåge dine Azure programmer til at kontrollere, at de er sund og fungerer som forventet ved at:

- Om oprettelse af nogle oprindelige måleredskaber for programmet, som gør det muligt at sammenligne aktuelle data og identificere eventuelle større ændringer i funktionsmåden for Azure lager og dit program. Værdierne af din oprindelige målepunkter bliver i mange situationer bestemt program, og du skal oprette dem, når du er performance-test dit program.
- Optage minut målepunkter og bruge dem til at overvåge aktivt for uventet fejl og afvigelser som spidser fejl tæller eller anmode om satser.
- Optage hver time målepunkter og bruge dem til at overvåge middelværdierne såsom gennemsnitlig fejl tæller og anmode om satser.
- Ved at undersøge potentielle problemer ved hjælp af diagnosticeringsværktøjer, som beskrives senere i afsnittet "[diagnosticere lagerplads problemer]".

Diagrammer i figur 3 nedenfor illustrerer, hvordan gennemsnittet, der opstår på hver time målepunkter kan skjule spidser i aktivitet. Hver time målepunkter ser ud til at vise et konstant antal anmodninger, mens du minuttet målepunkter afsløre udsving, der virkelig finder sted.

![][3]

Resten af dette afsnit beskriver, hvad du skal overvåge målepunkter og hvorfor.

### <a name="monitoring-service-health"></a>Overvågning af tjenestetilstand

Du kan bruge [Azure klassisk Portal](https://manage.windowsazure.com) til at få vist tilstanden for tjenesten Storage (og andre Azure tjenester) i alle Azure områder over hele verden. Dette gør det muligt at se med det samme, hvis et problem uden for dit kontrolelement påvirker tjenesten Storage i det område, du bruger til dit program.

Portalen Azure klassisk kan også give meddelelser om hændelser, der påvirker de forskellige Azure tjenester.
Bemærk: Disse oplysninger var tidligere tilgængelig sammen med historiske data på dashboardet til Azure Service på <a href="http://status.azure.com" target="_blank">http://status.azure.com</a>.

Mens portalen Azure klassisk indsamler sundhed oplysninger fra i de Azure datacentre (inde fra overvågning), kan du også overveje at træffe en udvendig i metode for at generere korte transaktioner, som med jævne mellemrum adgang til din Azure-hostet webprogram fra flere steder. De tjenester, <a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">Keynote</a>, <a href="https://www.gomeznetworks.com/?g=1" target="_blank">Gomez</a>og programmet indsigt til Visual Studio Team Services er eksempler på denne udvendig i metode. Du kan finde flere oplysninger om programmet indsigt til Visual Studio Team Services se tillægget "[tillæg 5: overvågning med programmet indsigt til Visual Studio Team Services]."

### <a name="monitoring-capacity"></a>Overvågning kapacitet

Gemmer kun kapacitet målepunkter for tjenesten blob Lagringsmål, fordi BLOB typisk højde for den største andel af lagrede data (på tidspunktet for skrivning, det er ikke muligt at bruge Lagringsmål til at overvåge kapaciteten i tabeller og køer). Du kan finde disse data i tabellen **$MetricsCapacityBlob** , hvis du har aktiveret overvågning for tjenesten Blob. Lagringsmål registrerer disse data en gang om dagen, og du kan bruge værdien af **RowKey** til at fastslå, om rækken indeholder et objekt, der relaterer til brugerdata (værdi **data**) eller analytics-data (værdi **analytics**). Hver gemt enhed indeholder oplysninger om mængden lagerplads bruges (**kapacitet** målt i byte) og det aktuelle antal beholdere (**ContainerCount**) og BLOB (**ObjectCount**) i brug i kontoen lagerplads. Se <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Lagerplads Analytics målepunkter tabelskema</a> på MSDN kan finde flere oplysninger om kapacitet målene gemt i tabellen **$MetricsCapacityBlob** .

> [AZURE.NOTE] Du bør overvåge disse værdier for en tidlig advarsel om, at du nærmer kapacitetsgrænser for kontoen lagerplads. Du kan tilføje regler for påmindelser for at give dig besked, hvis samlede lagerplads Brug overstiger eller falder under tærskler, du angiver i portalen Azure klassisk, på siden **skærm** for kontoen lagerplads.

For at få hjælp vurderer størrelsen på forskellige lagerplads objekter som blob, kan du se blogindlægget <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">Om Azure lagerplads fakturering – båndbredde, transaktioner, og kapacitet</a>.

### <a name="monitoring-availability"></a>Overvågning tilgængelighed

Du bør overvåge tilgængeligheden af tjenesterne lagerplads på din lagerplads konto ved at overvåge værdien i kolonnen **tilgængelighed** i tabellerne hver time eller minut målepunkter – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Kolonnen **tilgængelighed** indeholder en værdi i procent, som angiver tilgængeligheden af tjenesten eller handlingen API, der er repræsenteret af rækken ( **RowKey** viser Hvis rækken indeholder målepunkter for tjenesten som helhed eller for en bestemt API-operation).

En hvilken som helst værdi, der er mindre end 100% angiver, at nogle lagerplads anmodninger mislykkes. Du kan se, hvorfor de mislykkes ved at undersøge de øvrige kolonner i de målepunkter data, der viser antallet anmodninger med forskellige fejltyper som **ServerTimeoutError**. Du kan forvente at se **tilgængelighed** falder midlertidigt under 100% årsager som midlertidige servertimeout mens tjenesten flytter partitioner til bedre belastning anmodning forsøg i klientprogrammet skal håndtere sådanne forbigående betingelser. Siden <a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a> viser de filtyper, transaktion, som indeholder Lagringsmål i **tilgængelighed** beregningen.

Du kan tilføje regler for påmindelser for at give dig besked, hvis **tilgængelighed** til en tjeneste falder under en grænse, du angiver i portalen Azure klassisk, på siden **skærm** for kontoen lagerplads.

Afsnittet "[fejlfinding vejledning]" i denne vejledning beskrives nogle almindelige lagerplads serviceproblemer vedrører tilgængelighed.

### <a name="monitoring-performance"></a>Overvågning af ydeevnen

For at overvåge ydeevnen af lagerplads tjenesterne, kan du bruge følgende målepunkter fra tabellerne hver time og minut målepunkter.

- Værdierne i felterne **AverageE2ELatency** og **AverageServerLatency** vise den gennemsnitlige tid tjenesten storage eller API handlingstype går behandling af anmodninger om. **AverageE2ELatency** er en måling af til slut ventetid, der indeholder den tid, det tager at læse anmodningen og sende svaret ud over den tid, det tager at behandle anmodningen (derfor inkluderer netværksventetid når anmodningen når tjenesten storage); **AverageServerLatency** er en måling af lige behandling klokkeslættet, og derfor indeholder ikke en hvilken som helst netværksventetid, der er relateret til at kommunikere med klienten. Se afsnittet "[målepunkter vise høj AverageE2ELatency og lav AverageServerLatency]" senere i denne vejledning for en beskrivelse af, hvorfor der kan være en betydeligt forskellen mellem disse to værdier.
- Værdierne i kolonnerne **TotalIngress** og **TotalEgress** viser den samlede mængde af data, i byte, strømmer og af din lagringstjeneste eller via en bestemt type af API handling.
- Værdierne i kolonnen **TotalRequests** viser det samlede antal anmodninger, der modtager tjenesten storage af API handling. **TotalRequests** er det samlede antal anmodninger, tjenesten storage modtager.

Typisk, du vil overvåge til uventede ændringer i nogen af disse værdier som en indikator, at du har et problem, der kræver undersøgelse.

Du kan tilføje regler for påmindelser for at give dig besked, hvis nogen af ydeevnen målene for denne tjeneste falde under eller overskrider en grænse, du angiver i portalen Azure klassisk, på siden **skærm** for kontoen lagerplads.

Afsnittet "[fejlfinding vejledning]" i denne vejledning beskrives nogle almindelige lagerplads serviceproblemer i forbindelse med ydeevnen.


## <a name="diagnosing-storage-issues"></a>Diagnosticere lagerplads problemer

Der er flere måder, du kan blive opmærksom på et problem eller problem i programmet, dette omfatter:

- En overordnet fejl, der medfører, at programmet går ned eller stopper med at fungere.
- Væsentlige ændringer fra oprindelige værdier i målene du overvåger som beskrevet i forrige afsnit "[overvågning din lagringstjeneste]".
- Rapporter fra brugere af dit program, der ikke fuldfører en bestemt handling, som forventet eller, at nogle funktioner ikke fungerer.
- Fejl, der genereres i dit program, der vises i logfiler eller via en anden afskrivningsmetode, meddelelse.

Typisk problemer relateret til Azure lagerplads services falder inden for en af fire overordnede kategorier:

- Dit program har ydelsesproblemer, enten dine brugere har rapporteret, eller konstateret ved ændringer i ydeevne målepunkter.
- Der er et problem med Azure-lager infrastruktur i en eller flere områder.
- Dit program opfatter det som en fejl, enten dine brugere har rapporteret, eller konstateret ved en stigning i en af fejl Tæl målepunkter du overvåge.
- Under udvikling og test, kan du bruge lokale lager emulatoren; Du kan støde på nogle problemer, der specifikt vedrører brugen af emulatoren lagerplads.

De følgende afsnit beskrives de trin, du skal følge at diagnosticere og løse problemer i hver af disse fire kategorier. I afsnittet "[fejlfinding vejledning]" senere i denne vejledning indeholder flere detaljer for nogle almindelige problemer, der kan opstå.

### <a name="service-health-issues"></a>Service sundhed problemer

Problemer med tjenestetilstand er typisk uden for dit kontrolelement. Portalen Azure klassisk indeholder oplysninger om de igangværende problemer med Azure tjenesterne, herunder lagerplads tjenester. Hvis du valgt for læseadgang geografisk overflødige lagerplads, når du har oprettet kontoen lagerplads, kunne derefter i tilfælde af dine data, der ikke tilgængelig i den primære placering dit program skifte midlertidigt til skrivebeskyttet kopi i den sekundære placering. For at gøre dette, skal dit program kunne skifte mellem at bruge de primære og sekundære lagerplads placeringer, og være i stand til at arbejde i en begrænset funktionstilstand med skrivebeskyttede data. Azure-lager klient biblioteker kan bruges til at definere en politik for forsøg igen, som kan læse fra sekundær lagerplads, i tilfælde af et, der læses fra primær storage mislykkes. Dit program skal også være opmærksom på, at dataene i den sekundære placering til sidst ensartet. Du kan finde flere oplysninger, kan du se blogindlægget <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">Azure indstillinger for lagring af redundans og læseadgang geografisk overflødige lager</a>.

### <a name="performance-issues"></a>Problemer med ydeevnen

Ydeevnen for et program kan være subjektiv, især fra en bruger perspektiv. Det er derfor vigtigt at have oprindelige målepunkter kan hjælpe dig med at identificere, hvor der kan være problemer med ydeevnen. Mange faktorer kan påvirke ydeevnen for en Azure lagringstjeneste fra klient programmet perspektiv. Disse faktorer kan fungere i tjenesten storage, i klienten eller i netværksinfrastruktur. det er derfor vigtigt, at du har en strategi til at identificere origin af problemet med ydeevnen.

Når du har identificeret sandsynligvis placeringen af årsagen til problemet med ydeevnen fra målene, kan du derefter bruge logfilerne til at finde detaljerede oplysninger til at diagnosticere og foretage fejlfinding af problemet yderligere.

Afsnittet "[fejlfinding vejledning]" senere i denne vejledning giver flere oplysninger om nogle almindelige ydeevne i forbindelse problemer du kan støde på.

### <a name="diagnosing-errors"></a>Diagnosticere fejl

Brugere af dit program kan give dig besked om fejl, som rapporteres af klientprogrammet. Lagringsmål poster også optællinger for de forskellige fejltyper fra din lagerplads tjenester som **NetworkError**, **ClientTimeoutError**eller **AuthorizationError**. Mens Lagringsmål poster kun optællinger for de typer af anden fejl, kan du kan få flere oplysninger om individuelle anmodninger om ved at undersøge serversiden, klientsiden og netværk logfiler. Typisk giver den returnerede af tjenesten lagerplads HTTP-statuskode angivelse af, hvorfor anmodningen mislykkedes.

> [AZURE.NOTE] Husk, at du kan forvente at se nogle forbigående fejl: eksempelvis fejl på grund af midlertidige netværk betingelser eller programfejl.

I følgende ressourcer på MSDN er nyttige for at forstå lagerplads beslægtede status og fejlkoder:

- <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Almindelige RESTEN API fejlkoder</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">BLOB Service fejlkoder</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">Kø Service fejlkoder</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">Tabel-tjenesten fejlkoder</a>

### <a name="storage-emulator-issues"></a>Lagerplads emulator problemer

Azure SDK indeholder en lagerplads emulator du kan køre på en udvikling arbejdsstation. Denne emulator simulerer de fleste af funktionsmåden for tjenesterne Azure-lager og er nyttigt under udvikling og test, så du kan køre programmer, der bruger Azure lagerplads tjenester uden brug af et Azure-abonnement og en Azure-lager-konto.

Afsnittet "[fejlfinding vejledning]" i denne vejledning beskrives nogle almindelige problemer, der opstår ved hjælp af emulatoren lagerplads.

### <a name="storage-logging-tools"></a>Lagerplads logføring værktøjer

Lagerplads logføring giver serversiden logføring af anmodninger om lagerplads på din konto Azure-lager. Finde flere oplysninger om, hvordan du kan aktivere logføring på serversiden og access dataene i logfilen <a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">ved hjælp af serversiden logføring</a> på MSDN.

Biblioteket lagerplads klient til .NET gør det muligt at indsamle klientsiden logdata, der relaterer til lagerplads operationer i dit program. Finde flere oplysninger om, hvordan du kan aktivere logføring på klientsiden og access dataene i logfilen <a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">klientsiden logføring ved hjælp af biblioteket lagerplads klient</a> på MSDN.

> [AZURE.NOTE] I nogle tilfælde (såsom SAS godkendelse fejl), kan en bruger rapportere en fejl, som du kan finde nogen anmodning om data i loggene på serversiden lagerplads. Du kan bruge logføring funktionerne i biblioteket lagerplads klienten til at undersøge, om det af problemet skyldes på klienten eller bruge netværk overvågning værktøjer til at undersøge netværket.

### <a name="using-network-logging-tools"></a>Ved hjælp af netværk logføring værktøjer

Du kan hente trafik mellem klienten og serveren med detaljerede oplysninger om de data, udveksling af klienten og serveren og de underliggende netværk betingelser. Nyttige netværk logføring værktøjer omfatter:

- Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) er en gratis web fejlfinding proxy, der gør det muligt at undersøge sidehoveder og dataene i HTTP og HTTPS-meddelelser til og svar. Kan finde flere oplysninger under "[tillæg 1: Brug af Fiddler til at registrere HTTP og HTTPS trafik]".
- Microsoft Network Monitor (Netmon) (<a href="http://www.microsoft.com/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/download/details.aspx?id=4865</a>) og Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) er gratis netværk protocol programmer til analyse, der gør det muligt at få vist detaljerede pakkeoplysninger til en bred vifte af netværksprotokoller. Finde flere oplysninger om Wireshark "[tillæg 2: Brug af Wireshark til at registrere netværkstrafik]".
- Microsoft meddelelse Analyzer er et værktøj fra Microsoft, der tilsidesætter Netmon og, ud over at indsamle pakke netværksdata, hjælper dig med at få vist og analysere dataene i logfilen hentet fra andre værktøjer. Kan finde flere oplysninger under "[tillæg 3: Brug af Microsoft meddelelse Analyzer til at registrere netværkstrafik]".
- Hvis du vil udføre en grundlæggende forbindelsestesten for at kontrollere, at klientcomputeren kan oprette forbindelse til tjenesten Azure storage via netværket, kan du gøre dette ved hjælp af værktøjet standard **ping** på klienten. Dog kan du bruge værktøjet **tcping** til at kontrollere connectivity. **Tcping** kan hentes på <a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a>.

Logdata fra lagerplads logføring og biblioteket lagerplads klient er tilstrækkelige til at diagnosticere et problem i mange tilfælde, men i nogle tilfælde kan du muligvis mere detaljerede oplysninger, som kan give redskaberne netværk logføring. For eksempel bruge Fiddler til at få vist HTTP og HTTPS meddelelser gør det muligt at få vist headeren og dataene data, der er sendt til og fra de lagringstjenester,, som vil gør det muligt at undersøge, hvordan en klientprogrammet nye forsøg lagerplads handlinger. Protocol programmer til analyse som Wireshark betjene på niveauet pakke, så du kan få vist TCP-data, der ville gør det muligt at foretage fejlfinding af mistede pakker og problemer med serverforbindelsen. Meddelelse Analyzer kan fungere på både HTTP- og TCP lag.

## <a name="end-to-end-tracing"></a>Sporing af til slut

Sporing af til slut ved hjælp af en række forskellige logfiler er en praktisk teknik til ved at undersøge potentielle problemer. Du kan bruge dato/klokkeslæt-oplysninger fra dataene målepunkter som angivelse af, hvor skal begynde at søge i logfiler for de detaljerede oplysninger, der kan hjælpe dig med at løse problemet.

### <a name="correlating-log-data"></a>Korrelerende logdata

Når du får vist logfiler fra klientprogrammer, sporer netværk, og det er vigtigt at kunne koordinere logføring på serversiden lagerplads anmoder om på tværs af de forskellige logfiler. Logfilerne indeholder en række forskellige felter, der er nyttige som korrelations-id'er. Klient anmodnings-id er mest effektiv feltet der skal bruges til at koordinere poster i forskellige logfiler. Men nogle gange kan det være nyttigt at bruge enten server anmodnings-id eller tidsstempler. De følgende afsnit indeholder flere oplysninger om disse indstillinger.

### <a name="client-request-id"></a>Klient anmodnings-ID

Biblioteket lagerplads klient genererer automatisk et entydigt klient anmodnings-id for hver anmodning.

- Klient anmodnings-id vises i den logfil over klientsiden, biblioteket lagerplads klienten opretter, i feltet **Klient anmodnings-ID** for hver post i logfilen vedrørende anmodningen.
- I en netværkssporing som en fået, ved Fiddler vises klient anmodnings-id i anmodning om meddelelser som værdien **x-ms-klient-anmodning-id** HTTP sidehoved.
- Klient anmodnings-id vises i serversiden lagerplads logføring loggen, i kolonnen klient anmodning-ID.

> [AZURE.NOTE]Det er muligt for flere anmodninger om at dele den samme klient-anmodnings-id, fordi klienten kan tildele denne værdi (men biblioteket lagerplads klient tildeles automatisk en ny værdi). I tilfælde af gentagne forsøg, fra klienten dele alle forsøg på samme klient anmodnings-id. Hvis det er en gruppe, der sendes fra klienten, batchen et enkelt klient anmodnings-id.


### <a name="server-request-id"></a>Server anmodnings-ID

Tjenesten storage genererer automatisk server anmodning id'er.

- Server anmodnings-id vises i serversiden lagerplads logføring loggen, kolonnen **anmodnings-ID sidehoved** .
- Server anmodnings-id vises i en netværkssporing som en fået, ved Fiddler i svarmeddelelser som værdien **x-ms-anmodning-id** HTTP sidehoved.
- Server anmodnings-id vises i den logfil over klientsiden, biblioteket lagerplads klienten opretter, i kolonnen **Handling tekst** for posten i logfilen med oplysninger om svaret fra serveren.

> [AZURE.NOTE] Tjenesten storage tildeler altid en entydig server anmodnings-id til hver anmodning om den modtager, så alle forsøg på at fra klienten på computeren og hver funktion, der er inkluderet i en gruppe har et entydigt server anmodnings-id.

Hvis biblioteket lagerplads klient viser en **StorageException** i klienten, indeholder egenskaben **RequestInformation** et **RequestResult** objekt, der indeholder en **ServiceRequestID** egenskab. Du kan også få adgang til et **RequestResult** objekt fra en **OperationContext** forekomst.

Kodeeksemplet nedenfor viser, hvordan til at angive en brugerdefineret **ClientRequestId** værdi ved at knytte et **OperationContext** objekt anmodningen til tjenesten storage. Det også viser, hvordan du henter værdien **ServerRequestId** fra svarmeddelelsen.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>Tidsstempler

Du kan også bruge tidsstempler Find relaterede logposter, men vær forsigtig med en hvilken som helst ur skævhed mellem klienten og serveren, der kan findes. Du skal søge plus eller minus 15 minutter efter tilsvarende serversiden poster baseret på tidsstemplet på klienten. Husk, at blob metadataene for BLOB med målepunkter angiver tidsintervallet på målepunkter gemt i blob Dette er nyttigt, hvis du har mange målepunkter BLOB til samme minut eller time.

## <a name="troubleshooting-guidance"></a>Vejledning til fejlfinding

Dette afsnit kan hjælpe dig med diagnosticering og fejlfinding for nogle af de almindelige problemer dit program kan opstå, når du bruger tjenesterne Azure-lager. Brug listen nedenfor til at finde de oplysninger, der er relevante for dit specifikke problem.

**Fejlfinding i forbindelse med beslutningstræ**

----------

Dit problem, der er relateret til ydeevnen for en af lagerplads-tjenester?

- [Målepunkter vise høj AverageE2ELatency og lav AverageServerLatency]
- [Målepunkter vise lav AverageE2ELatency og lav AverageServerLatency, men klienten oplever lang ventetid]
- [Målepunkter Vis høj AverageServerLatency]
- [Du oplever uventede forsinkelser i levering af meddelelser på en kø]

----------

Dit problem, der er relateret til tilgængeligheden af en af lagerplads-tjenester?

- [Målepunkter vise en stigning i PercentThrottlingError]
- [Målepunkter vise en stigning i PercentTimeoutError]
- [Målepunkter vise en stigning i PercentNetworkError]

----------

Modtager klientprogrammet et HTTP-4XX (såsom 404) svar fra en lagringstjeneste?

- [Klienten modtager HTTP 403 (forbudt) meddelelser]
- [Klienten modtager HTTP 404 (blev ikke fundet) meddelelser]
- [Klienten modtager HTTP 409 (konflikt) meddelelser]

----------

[Målepunkter vise lav PercentSuccess eller analytics logposter have handlinger med transaktionsstatus af ClientOtherErrors]

----------

[Kapacitet målepunkter vise en uventede stigning i kapacitet lagerforbrug]

----------

[Du oplever uventede genstarter af virtuelle maskiner, der har et stort antal vedhæftede virtuelle harddiske]

----------

[Dit problem opstår i at bruge lagerplads emulatoren til udvikling eller test]

----------

[Du oplever problemer med at installere Azure SDK til .NET]

----------

[Du har et andet problem med en lagringstjeneste]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Målepunkter vise høj AverageE2ELatency og lav AverageServerLatency

Illustration blæse fra værktøjet Azure klassisk Portal overvågning viser et eksempel, hvor **AverageE2ELatency** er væsentligt højere end **AverageServerLatency**.

![][4]

Bemærk, at tjenesten storage kun beregner metrisk **AverageE2ELatency** vellykket anmodninger om og, i modsætning til **AverageServerLatency**, indeholder den tid, der tager klienten til at sende dataene og modtage bekræftelse fra tjenesten lagerplads. Forskel på **AverageE2ELatency** og **AverageServerLatency** kan derfor på grund af klientprogrammet der reagerer langsomt eller på grund af betingelser på netværket.

> [AZURE.NOTE] Du kan også få vist **E2ELatency** og **ServerLatency** for individuelle lagerplads handlinger i dataene i lagerplads logføring logfilen.

#### <a name="investigating-client-performance-issues"></a>Ved at undersøge problemer med ydeevnen af klientcomputer

Mulige årsager til klienten reagerer langsomt omfatter har et begrænset antal tilgængelige forbindelser eller tråde. Du kan muligvis løse problemet ved at ændre Klientkoden for at være mere effektiv (for eksempel ved hjælp af asynkrone opkald til tjenesten storage) eller ved hjælp af en større virtuel maskine (med flere kerner og mere hukommelse).

For tabeller og kø tjenesterne, algoritmen Nagle også kan medføre høj **AverageE2ELatency** i forhold til **AverageServerLatency**: finde flere oplysninger i indlægget <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">Nagles algoritme er ikke fuldt med små anmodninger</a> på Microsoft Azure-lager Team Blog. Du kan deaktivere algoritmen Nagle kode ved hjælp af klassen **ServicePointManager** i navneområdet **System.Net** . Du skal gøre dette, før du foretager et opkald til tabellen eller kø services i dit program, da dette ikke påvirker forbindelser, der allerede er åbne. I følgende eksempel kommer fra metoden **Application_Start** i en kollega rolle.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Du skal kontrollere loggene på klientsiden til at se antallet af anmodninger om afsendelse af klientprogrammet, og Kontrollér, om generelle .NET relateret flaskehalse for performance i kunden som CPU, .NET Spildopsamling, udnyttelse af netværk eller hukommelse (som et udgangspunkt til at løse .NET klientprogrammer skal du se <a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">fejlfinding, sporing, og profiler</a> på MSDN).

#### <a name="investigating-network-latency-issues"></a>Ved at undersøge ventetid netværksproblemer

Lang til slut ventetid skyldes netværket er typisk på grund af midlertidige betingelser. Du kan undersøge begge midlertidige og fast netværksproblemer som mistede pakker ved hjælp af funktioner som Wireshark eller Microsoft meddelelse Analyzer.

Finde flere oplysninger om brug af Wireshark til fejlfinding i netværk, "[tillæg 2: Brug af Wireshark til at registrere netværkstrafik]."

Finde flere oplysninger om brug af Microsoft meddelelse Analyzer til fejlfinding i netværk, "[tillæg 3: Brug af Microsoft Message Analyzer til at registrere netværkstrafik]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Målepunkter vise lav AverageE2ELatency og lav AverageServerLatency, men klienten oplever lang ventetid

I dette scenario er den mest sandsynlige årsag en forsinkelse i at kontakte tjenesten storage lagerplads anmodningerne. Du bør undersøge, hvorfor anmodninger fra klienten ikke laver igennem til blob-tjenesten.

Mulige årsager til klienten forsinke afsendelse af anmodninger om omfatter har et begrænset antal tilgængelige forbindelser eller tråde. Du skal også kontrollere Hvis klienten udfører flere forsøg og undersøge årsagen til, hvis det er tilfældet. Du kan gøre dette ved hjælp af programmering ved søger i objektet **OperationContext** , der er knyttet til anmodningen og hentning af værdien **ServerRequestId** . Du kan finde flere oplysninger, se kodeeksemplet i afsnittet "[Server anmodnings-ID]".

Hvis der er ingen problemer i klienten, bør du undersøge potentielle netværksproblemer som pakketab. Du kan bruge funktioner som Wireshark eller Microsoft meddelelse Analyzer til at undersøge netværksproblemer.

Finde flere oplysninger om brug af Wireshark til fejlfinding i netværk, "[tillæg 2: Brug af Wireshark til at registrere netværkstrafik]."

Finde flere oplysninger om brug af Microsoft meddelelse Analyzer til fejlfinding i netværk, "[tillæg 3: Brug af Microsoft Message Analyzer til at registrere netværkstrafik]."

### <a name="metrics-show-high-AverageServerLatency"></a>Målepunkter Vis høj AverageServerLatency

I forbindelse med høj **AverageServerLatency** blob download anmodninger om, skal du bruge loggene lagerplads logføring til at se, om der er gentagne anmodninger om den samme blob (eller et sæt af BLOB). Overføre anmodninger til blob, bør du undersøge, hvilke Bloker størrelse klienten bruger (for eksempel blokerer mindre end 64 KB i størrelse, kan medføre omkostninger, medmindre læser er også i mindre end 64 KB stykker), og hvis flere klienter sender dokumentkomponenter til den samme blob parallelt. Du bør også kontrollere-minutters omfanget af spidser i antallet af anmodninger, der medfører overskridelse på hver anden skalerbarhed destinationer: også få vist "[målepunkter vise et større PercentTimeoutError]".

Hvis du får vist høj **AverageServerLatency** for blob hente anmodninger, når der er gentagne anmodninger om den samme blob eller gruppe af blob, skal derefter du overveje cachelagring disse BLOB ved hjælp af Azure Cache eller Azure indhold levering netværk (CDN). Du kan forbedre overførselshastigheden ved hjælp af en større Bloker Overfør anmodninger om. For forespørgsler til tabeller er det også muligt at implementere klientsiden cache for klienter, som udfører samme forespørgslerne og hvor dataene ikke ændres ofte.

Høj **AverageServerLatency** værdier kan også være et symptom på dårligt designet tabeller eller forespørgsler, at resultatet i scanningen, eller som følger Føj/føjes mod mønstret. Yderligere oplysninger finder du i "[målepunkter vise et større PercentThrottlingError]".

> [AZURE.NOTE] Du kan finde en omfattende tjekliste ydeevne tjekliste her: [Microsoft Azure lagerplads ydeevne og skalerbarhed tjekliste](storage-performance-checklist.md).

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Du oplever uventede forsinkelser i levering af meddelelser på en kø

Hvis du oplever en forsinkelse mellem den tid, der er et program tilføjer en meddelelse til en kø og den tid, det bliver muligt at læse fra køen, skal du tage følgende trin for at diagnosticere problemet:

- Kontrollér programmet korrekt føjer meddelelser til køen. Kontrollér, at programmet ikke er forsøger igen metoden **AddMessage** flere gange før efterfølgende. Lagerplads klientbibliotek logfilerne viser eventuelle gentagne forsøg i forbindelse med lagring.
- Bekræft, der ikke er nogen ur skævhed mellem rollen arbejder, der tilføjer meddelelsen til køen og rollen arbejder, som læser meddelelsen fra køen, der gør det vises som om der er en forsinkelse i behandling.
- Markér, hvis rollen arbejder, som læser meddelelser fra køen går ned. Hvis en kø klient kalder metoden **GetMessage** , men ikke kan svare med en bekræftelse, forbliver meddelelsen usynlige i køen, indtil den **invisibilityTimeout** udløber. På dette tidspunkt bliver meddelelsen tilgængelig til at behandle igen.
- Markér, hvis længde kø vokser over tid. Dette kan ske, hvis du ikke har tilstrækkelige medarbejdere, der er tilgængelige til at behandle alle de meddelelser, som andre medarbejdere markedsføring køen. Du skal også kontrollere målepunkter at se Hvis Slet anmodninger om fejlbehæftede og dequeue antallet på meddelelser, som viser gentages mislykkede forsøg på at slette meddelelsen.
- Undersøge loggene lagerplads logføring til en hvilken som helst kø handlinger, som har højere end forventet **E2ELatency** og **ServerLatency** værdier over en længere periode end normalt.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Målepunkter vise en stigning i PercentThrottlingError

Variere den benyttede fejl opstår, når du overskrider basis af en lagringstjeneste skalerbarhed mål. Tjenesten storage gør dette for at sikre, at ingen enkelt klient eller lejer kan bruge tjenesten fra andre. I <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Azure lagerplads skalerbarhed og ydeevne destinationer</a> oplysninger om skalerbarhed mål for lagerplads konti og ydeevne mål for partitioner i lagerplads konti kan finde flere oplysninger.

Hvis **PercentThrottlingError** metrisk viser en stigning i procent af forespørgsler, der ned med en båndbreddebegrænsning fejl, skal du undersøge en af to scenarier:

- [Midlertidige stigning i PercentThrottlingError]
- [Permanent stigning i PercentThrottlingError fejl]

Et større **PercentThrottlingError** forekommer ofte på samme tid som en stigning i antallet af anmodninger om lagerplads, eller når du er først indlæse test dit program. Dette kan også forekomme i klienten som "503 Server optaget" eller "500 handlingen Timeout" HTTP statusmeddelelser fra lagerplads handlinger.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Midlertidige stigning i PercentThrottlingError

Hvis du får vist spidser i værdien af **PercentThrottlingError** , der falder sammen med perioder med høj aktivitet for programmet, du skal implementere en eksponentiel (ikke-lineær) tilbage fra strategi for nye forsøg i din klient: Dette vil reducere den øjeblikkelige belastning på partitionen og hjælpe programmet udjævner spidser i trafik. Du kan finde flere oplysninger om, hvordan du implementere forsøg politikker ved hjælp af biblioteket lagerplads klient, kan du se <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">Microsoft.WindowsAzure.Storage.RetryPolicies Namespace</a> på MSDN.

> [AZURE.NOTE] Du kan også få vist spidser i værdien af **PercentThrottlingError** , der ikke falder sammen med perioder med høj aktivitet for programmet: den mest sandsynlige årsag er tjenesten storage flytte partitioner til at forbedre belastning.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Permanent stigning i PercentThrottlingError fejl

Hvis du får vist en ensartet måde høj værdi for **PercentThrottlingError** efter en permanent stigning i dine transaktion drev, eller når du udfører din oprindelige last tester på dit program, er du nødt til at evaluere, hvordan dit program bruger lagerplads partitioner og om det nærmer skalerbarhed mål for en lagerplads konto. Eksempelvis hvis du får vist, throttling fejl på en kø (som tæller som en enkelt partition), derefter du bør overveje at bruge flere køer til at sprede transaktionerne på tværs af flere partitioner. Hvis du får vist, throttling fejl på en tabel, skal du overveje at bruge et andet leverandør farveskema til at sprede dine transaktioner på tværs af flere partitioner ved hjælp af en lang række partitionsnøgleværdier. En almindelig årsag til dette problem er prepend/Føj mod mønstret sted, hvor du vælger datoen som tasten partition og derefter alle data i en bestemt dag skrives til én partition: belastning, kan det resultere i en Skriv flaskehals. Du bør overveje et andet leverandør design eller evaluere om brug af blob-lager kan være en bedre løsning. Du skal også Markér, hvis den (throttling) bliver til virkelighed som et resultat af spidser i din trafik og undersøge måder udglatning din anvendelse af anmodninger om.

Hvis du distribuerer dine transaktioner på tværs af flere partitioner, skal du stadig være opmærksom på skalerbarhed begrænsningerne for kontoen lagerplads. Eksempelvis hvis du brugte ti køer behandling maksimalt antal 2000 1KB meddelelser sekundet, bliver du på overordnede grænsen på 20.000 meddelelser sekundet for kontoen lagerplads. Hvis du har brug at behandle mere end 20.000 enheder sekundet, bør du overveje at bruge flere lagerplads konti. Du skal også tage i betragtning, størrelsen på dine mødeindkaldelser og enheder har indflydelse på når tjenesten storage throttles dine kunder: Hvis du har større anmodninger om og enheder, du kan være begrænset hurtigere.

Ineffektiv forespørgselsdesign kan også medføre, at du at ramme skalerbarhed begrænsninger for tabel partitioner. For eksempel skal en forespørgsel med et filter, der kun markerer en procentdel af objekterne i en partition, men, der scanner alle objekterne i en partition få adgang til de enkelte objekter. Alle objekter læse vil regnes med det samlede antal transaktioner i denne partition Derfor kan du nemt når skalerbarhed destinationer.

> [AZURE.NOTE] Din test af ydeevne vil vise en hvilken som helst ineffektiv forespørgselsdesign i programmet.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Målepunkter vise en stigning i PercentTimeoutError

Din målepunkter vise en stigning i **PercentTimeoutError** for et af dine lagerplads tjenester. Klienten modtager en stor mængde "500 handlingen Timeout" HTTP statusmeddelelser fra lagerplads handlinger på samme tid.

> [AZURE.NOTE] Du kan se timeoutfejl midlertidigt som tjenesten storage indlæse saldi anmodninger ved at flytte en partition til en ny server.

**PercentTimeoutError** metrisk er en sammenlægning af de følgende målepunkter: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**og **SASServerTimeoutError**.

Servertimeout skyldes en fejl på serveren. Klient-timeout opstå, fordi en handling på serveren, der har overskredet den timeout, der er angivet af klienten; for eksempel kan en klient, ved hjælp af biblioteket lagerplads klient angive en timeout for en handling ved hjælp af egenskaben **ServerTimeout** i klassen **QueueRequestOptions** .

Servertimeout skyldes et problem med tjenesten storage, der kræver yderligere undersøgelse. Du kan bruge målepunkter, til at se, hvis du nå skalerbarhed begrænsningerne for tjenesten og identificere en hvilken som helst spidser i trafik, der kan forårsage dette problem. Hvis problemet er forbigående, kan det være på grund af belastning aktivitet i tjenesten. Hvis problemet er fast og skyldes ikke programmet nå skalerbarhed begrænsningerne for tjenesten, skal du hæve et support-spørgsmål. For klient timeout, skal du beslutte, hvis timeouten er indstillet til den ønskede værdi på klienten og enten ændre timeoutværdien angive i klienten eller undersøge, hvordan du kan forbedre ydeevnen for de handlinger i tjenesten lager ved at optimere dine tabel forespørgsler eller reducere størrelsen på dine meddelelser f.eks.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Målepunkter vise en stigning i PercentNetworkError

Din målepunkter vise en stigning i **PercentNetworkError** for et af dine lagerplads tjenester. **PercentNetworkError** metrisk er en sammenlægning af de følgende målepunkter: **NetworkError**, **AnonymousNetworkError**og **SASNetworkError**. Disse opstå, når tjenesten storage registrerer fejlen netværk, når klienten foretager en anmodning om lagerplads.

Den mest almindelige årsag til denne fejl er en klient afbryder før en timeout udløber i tjenesten lagerplads. I din klient at forstå, hvorfor og hvornår klienten afbryder forbindelsen tjenesten storage bør du undersøge koden. Du kan også bruge Wireshark, Microsoft meddelelse Analyzer eller Tcping for at undersøge problemer med netværksforbindelsen fra klienten på computeren. Disse funktioner er beskrevet i [tillæg].

### <a name="the-client-is-receiving-403-messages"></a>Klienten modtager HTTP 403 (forbudt) meddelelser

Hvis klientprogrammet er aktiverende HTTP 403 (forbudt) fejl, er en sandsynlige årsag, klienten anvendes et udløbet delt Access signatur (SAS), når der udsendes en anmodning om lagerplads (selvom andre mulige årsager kan være ur skævhed, ugyldige nøgler og tomme sidehoveder). Hvis en udløbet SAS nøgle er årsagen, se du ikke alle poster i serversiden lagerplads logføring logdata. I følgende tabel vises en stikprøve fra klientsiden logfilen genereres af biblioteket lagerplads klient, som illustrerer dette problem opstår:

Kilde|Oplysningerne|Oplysningerne|Klient anmodnings-id|Handlingen tekst
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Du kan finde oplysninger|3|85d077ab-...|Starter handlingen med placering primær per placering tilstand PrimaryOnly.
Microsoft.WindowsAzure.Storage|Du kan finde oplysninger|3|85d077ab-...|Starte synkron anmodning https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;api-version = 2014-02-14.
Microsoft.WindowsAzure.Storage|Du kan finde oplysninger|3|85d077ab-...|Venter på svar.
Microsoft.WindowsAzure.Storage|Advarsel|2|85d077ab-...|Undtagelse udløst under venter på svar: fjernserveren returneres en fejl: (403) forbudt..
Microsoft.WindowsAzure.Storage|Du kan finde oplysninger|3|85d077ab-...|Svaret. Statuskode = 403, anmode om ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, indhold MD5 = ETag =.
Microsoft.WindowsAzure.Storage|Advarsel|2|85d077ab-...|Undtagelse udløst under handlingen: fjernserveren returneres en fejl: (403) forbudt..
Microsoft.WindowsAzure.Storage|Du kan finde oplysninger|3 |85d077ab-...|Kontrollerer, hvis handlingen skal forsøges igen. Antal forsøg = 0, HTTP-statuskode = 403, undtagelse = den eksterne server, der returneres en fejl: (403) forbudt..
Microsoft.WindowsAzure.Storage|Du kan finde oplysninger|3|85d077ab-...|Den næste lokalitet er blevet indstillet til primær, baseret på tilstanden placering.
Microsoft.WindowsAzure.Storage|Fejl|1|85d077ab-...|Prøv igen politik gav ikke mulighed for et nyt forsøg. Fejlbehæftede med fjernserveren returneres en fejl: (403) forbudt.

I dette scenarie, bør du undersøge, hvorfor SAS tokenet udløber, før klienten sender tokenet til serveren:

- Typisk, skal du ikke angive et starttidspunkt, når du opretter en SAS for en klient til at bruge øjeblikkeligt. Hvis der er lille ur forskelle mellem værten genererende Sikkerhedstilknytningerne ved hjælp af det aktuelle klokkeslæt og tjenesten lagerplads, så det er muligt for tjenesten storage til at modtage en SAS, som ikke endnu er gyldig.
- Du skal ikke angive en meget kort udløbstid på en SAS. Igen, lille ur forskelle mellem værten for oprettelse af Sikkerhedstilknytningerne og tjenesten storage kan medføre en SAS tilsyneladende udløber tidligere end forventet.
- Indeholder versionsparameteren i nøglen SAS (for eksempel **sv = 2012-02-12**) svarer til versionen af biblioteket lagerplads klient, du bruger. Du bør altid bruge den nyeste version af biblioteket lagerplads klient. Du kan finde flere oplysninger om SAS token versionsstyring, kan du se [Hvad er nyt til Microsoft Azure-lager](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx).
- 
- Hvis du genoprette dine lagerplads adgangstaster (Klik på **Administrer Access-taster** på en side i din lagerplads konto i portalen Azure klassisk) kan det medføre, at alle eksisterende SAS tokens. Dette kan være et problem, hvis du opretter SAS tokens med en lang udløbet tid for cache-klientprogrammerne.

Hvis du bruger biblioteket lagerplads klienten til at generere SAS tokens, er det nemt at opbygge et gyldigt token. Hvis du bruger lagerplads REST-API og bygning af SAS tokens manuelt skal du omhyggeligt læse emnet <a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">Delegering af Access med en delt Access signatur</a> på MSDN.

### <a name="the-client-is-receiving-404-messages"></a>Klienten modtager HTTP 404 (blev ikke fundet) meddelelser
Hvis klientprogrammet modtager en meddelelse om HTTP 404 (blev ikke fundet) fra serveren, betyder det, at objektet klienten forsøgte at bruge (som et objekt, en tabel, blob, objektbeholder eller kø) ikke findes i tjenesten storage. Der er en række mulige årsager til dette, f.eks.:

- [Klienten eller en anden proces slettet objektet]
- [Et delt Access signatur (SAS) godkendelse problem]
- [Klientsiden JavaScript-kode har ikke tilladelse til at få adgang til objektet]
- [Netværk mislykkedes]

#### <a name="client-previously-deleted-the-object"></a>Klienten eller en anden proces slettet objektet
I scenarier, hvor klienten forsøger at læse, opdatere eller slette data i en lagringstjeneste er det som regel nemt at identificere en tidligere handling, slettes det pågældende objekt fra tjenesten storage i loggene på serversiden. Dataene i logfilen viser meget ofte, at en anden bruger eller proces slettet objektet. Serversiden lagerplads logføring log vises type handling og anmodet om-objekt-nøgle kolonner når en klient slettet et objekt.

I dette scenario, hvor en klient forsøger at indsætte et objekt, den muligvis ikke umiddelbart indlysende hvorfor dette resulterer i et svar med HTTP 404 (blev ikke fundet), da der klienten opretter et nyt objekt. Men hvis klienten opretter en blob, den skal kunne finde objektbeholderen blob Hvis klienten opretter en meddelelse, den skal kunne finde en kø, og hvis klienten er at tilføje en række den skal kunne finde tabellen.

Du kan bruge klientsiden loggen fra biblioteket lagerplads klienten til at få en mere detaljeret forståelse af når klienten sender specifikke anmodninger til tjenesten storage.

Følgende klientsiden logfilen genereres af biblioteket lagerplads klient illustrerer problemet, når klienten ikke kan finde beholderen til den opretter blob. Denne log indeholder oplysninger om lagerplads følgende handlinger:

Anmodnings-ID|Handling
---|---
07b26a5d-...|**DeleteIfExists** metode til at slette objektbeholderen blob. Bemærk, at denne handling anmodes om kvittering **i afsnit** for at kontrollere, om eksistensen af objektbeholderen.
e2d06d78...|**CreateIfNotExists** metode til at oprette objektbeholderen blob. Bemærk, at denne handling omfatter en **hoved** -anmodning, kontrollerer eksistensen af objektbeholderen. **Hoved** returnerer en 404 meddelelse, men stadig.
de8b1c3c-...|**UploadFromStream** metode til at oprette blob. **PLACERE** anmodningen mislykkes med en 404 meddelelse

Logposter:

Anmodnings-ID |  Handlingen tekst
---|---
07b26a5d-...|Starte synkron anmodning https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|Venter på svar.
07b26a5d-... | Svaret. Statuskode = 200, anmode om ID = eeead849... Indhold MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;.
07b26a5d-... | Svar sidehoveder blev behandlet, procedure med resten af handlingen.
07b26a5d-... | Hente svar brødtekst.
07b26a5d-... | Handlingen blev fuldført.
07b26a5d-... | Starte synkron anmodning https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | Venter på svar.
07b26a5d-... | Svaret. Statuskode = 202, anmode om ID = 6ab2a4cf-..., indhold MD5 = ETag =.
07b26a5d-... | Svar sidehoveder blev behandlet, procedure med resten af handlingen.
07b26a5d-... | Hente svar brødtekst.
07b26a5d-... | Handlingen blev fuldført.
e2d06d78-... | Starter asynkron anmodning til https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| Venter på svar.
de8b1c3c-... | Starte synkron anmodning https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... |  StringToSign = læg... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-BLOB-type:BlockBlob.x-MS-Client-Request-id:de8b1c3c-...x-MS-Date:TUE, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Forberedelse til at skrive anmodning om data.
e2d06d78-... | Undtagelse udløst under venter på svar: fjernserveren returneres en fejl: (404) blev ikke fundet..
e2d06d78-... | Svaret. Statuskode = 404-fejlmeddelelse, anmode om ID = 353ae3bc-..., indhold MD5 =, ETag =.
e2d06d78-... | Svar sidehoveder blev behandlet, procedure med resten af handlingen.
e2d06d78-... | Hente svar brødtekst.
e2d06d78-... | Handlingen blev fuldført.
e2d06d78-... | Starter asynkron anmodning til https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = læg... 0...x-MS-Client-Request-id:e2d06d78-...x-MS-Date:TUE, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | Venter på svar.
de8b1c3c-... | Skrive anmodning om data.
de8b1c3c-... | Venter på svar.
e2d06d78-... | Undtagelse udløst under venter på svar: fjernserveren returneres en fejl: (409) konflikt..
e2d06d78-... | Svaret. Statuskode = 409, anmode om ID = c27da20e-..., indhold MD5 = ETag =.
e2d06d78-... | Hente fejl svar brødtekst.
de8b1c3c-... | Undtagelse udløst under venter på svar: fjernserveren returneres en fejl: (404) blev ikke fundet..
de8b1c3c-... | Svaret. Statuskode = 404-fejlmeddelelse, anmode om ID = 0eaeab3e-..., indhold MD5 =, ETag =.
de8b1c3c-...| Undtagelse udløst under handlingen: fjernserveren returneres en fejl: (404) blev ikke fundet..
de8b1c3c-... | Prøv igen politik gav ikke mulighed for et nyt forsøg. Fejlbehæftede med fjernserveren returneres en fejl: (404) blev ikke fundet..
e2d06d78-... | Prøv igen politik gav ikke mulighed for et nyt forsøg. Fejlbehæftede med fjernserveren returneres en fejl: (409) konflikt..

I dette eksempel viser loggen, kunden interleaving anmodninger fra metoden **CreateIfNotExists** (… anmodning-id e2d06d78) med anmodningerne fra metoden **UploadFromStream** (de8b1c3c-...); Dette sker, fordi klientprogrammet aktiverer asynkront disse metoder. Du skal ændre asynkron koden i klienten til at sikre, at den opretter objektbeholderen, før du forsøger at overføre data til en blob i objektbeholderen. Ideelt set skal du oprette alle beholdere i forvejen.

#### <a name="SAS-authorization-issue"></a>Et delt Access signatur (SAS) godkendelse problem

Hvis klientprogrammet forsøger at bruge en SAS nøgle, der ikke omfatter de nødvendige tilladelser til handlingen, returnerer tjenesten storage en HTTP 404 (blev ikke fundet) meddelelse til klienten. På samme tid, kan du også se værdien ikke er nul for **SASAuthorizationError** i målene.

I følgende tabel vises en meddelelse på serversiden log fra logfilen lagerplads logføring:

<table>
  <tr>
    <td>Anmode om starttidspunktet</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Handlingstype</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Anmod om status</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>HTTP-statuskode</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Godkendelsestype</td>
    <td>SAS</td>
  </tr>
  <tr>
    <td>Tjenestetype</td>
    <td>BLOB</td>
  </tr>
  <tr>
    <td>URL-anmodningen</td>
    <td>
    https://domemaildist.BLOB.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp; sr = c&amp;amp; si = mypolicy&amp;amp; sig = XXXXX&amp;amp; api-version = 2014-02-14&amp;amp;</td>
  </tr>
  <tr>
    <td>Anmode om id sidehoved</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>Klient-ID anmodning</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Du bør undersøge, hvorfor klientprogrammet forsøger at udføre en handling, der er det ikke har fået tildelt tilladelser til.

#### <a name="JavaScript-code-does-not-have-permission"></a>Klientsiden JavaScript-kode har ikke tilladelse til at få adgang til objektet

Hvis du bruger en JavaScript-klient og tjenesten storage returnerer HTTP 404 meddelelser skal du kontrollere, om følgende JavaScript fejl i browseren:

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] Du kan bruge F12-udviklerværktøjer i Internet Explorer til at spore de meddelelser, der er udvekslet i browseren og tjenesten storage, når du foretager fejlfinding af problemer med klientsiden JavaScript.

Disse fejl kan opstå, fordi webbrowseren implementerer den <a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">samme origin politik</a> sikkerhedsbegrænsning, der forhindrer, at ringe til en API i et andet domæne end det domæne, siden kommer fra en webside.

Du kan løse problemet JavaScript, kan du konfigurere Cross Origin ressource deling (CORS) for tjenesten lagerplads, der har adgang til klienten. Se <a href="http://msdn.microsoft.com/library/azure/dn535601.aspx" target="_blank">tværs Origin ressource deling (CORS) understøttelse af Azure lagerplads Services</a> på MSDN kan finde flere oplysninger.

Følgende eksempel-kode viser, hvordan du konfigurerer din blob tjeneste for at tillade JavaScript kører i Contoso domænet til at få adgang til en blob i din blob storage tjeneste:

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Netværk mislykkedes

I nogle tilfælde kan føre tabt netværkspakker til tjenesten storage returnere HTTP 404-meddelelser til kunden. For eksempel når klientprogrammet er sletning af et objekt fra tabeltjenesten kan du se klienten Udløs en lagerplads undtagelse rapportering en "HTTP 404 (blev ikke fundet)" statusmeddelelse fra tabeltjenesten. Når du undersøge tabellen i lagringstjeneste tabel, skal se du, tjenesten blev slettet objektet, som ønsket.

Undtagelse oplysningerne i klienten indeholder anmodnings-id (7e84f12d...) tildelt af tjenesten tabel for anmodningen: Du kan bruge disse oplysninger til at finde anmodning om oplysninger i loggene på serversiden lager ved at søge i kolonnen **anmodning-id-brevhoved** i logfilen. Du kan også bruge målene til at identificere når fejl som dette forekommer, og søg så efter de logfiler, der er baseret på det tidspunkt, målene registreret denne fejl. Denne post i logfilen viser, at afkrydsningsfeltet Slet mislykkedes, og meddelelsen "HTTP (404) klient andre fejl" status. Den samme post i logfilen indeholder også anmodnings-id genereres af klienten i kolonnen **kunde-anmodning-id** (813ea74f...).

Log på serversiden indeholder også en anden post med den samme **klient-anmodning-id** -værdi (813ea74f …) for en vellykket sletning for den samme enhed og fra den samme klient. Denne vellykket sletning fandt sted umiddelbart før den mislykkedes sletter anmodning.

Den mest sandsynlige årsag til dette scenario er, at klienten sendte en Slet anmodning for objektet til tjenesten tabel, som er fuldført, men ikke har modtaget en bekræftelse fra serveren (måske på grund af et midlertidigt netværksproblem). Klienten igen derefter automatisk handlingen (med det samme **klient-anmodning-id**), og denne forsøg mislykkedes, fordi objektet er blevet slettet.

Hvis dette problem opstår ofte, bør du undersøge, hvorfor klienten går ned til at modtage godkendelser fra tabeltjenesten. Hvis problemet er forbigående, skal du fange fejlen "HTTP (404) blev ikke fundet" og logge på klienten, men Tillad klienten til at fortsætte.

### <a name="the-client-is-receiving-409-messages"></a>Klienten modtager HTTP 409 (konflikt) meddelelser

Følgende tabel viser et uddrag fra serversiden logfilen for to klienthandlinger: **DeleteIfExists** umiddelbart foran **CreateIfNotExists** med det samme blob objektbeholder navn. Bemærk, at hver klient handlingen resultater i to anmodninger sendt til serveren, først en **GetContainerProperties** anmodning om at kontrollere, om findes objektbeholderen, efterfulgt af **DeleteContainer** eller **CreateContainer** anmodningen.

Tidsstempel|Handling|Resultat|Navnet på objektbeholderen|Klient anmodnings-id
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

Koden i klientprogrammet sletter og med det samme genopretter en blob objektbeholder med det samme navn: metoden **CreateIfNotExists** (klient anmodning ID bc881924-...) til sidst mislykkes med fejlen HTTP 409 (konflikt). Når en klient sletter blob beholdere, tabeller eller køer der er en kort periode før navnet bliver tilgængelig igen.

Klientprogrammet skal bruge entydige objektbeholder navne, når programmet opretter ny og ubrugt, hvis Slet/Genopret mønsteret er almindelige.

### <a name="metrics-show-low-percent-success"></a>Målepunkter vise lav PercentSuccess eller analytics logposter have handlinger med transaktionsstatus af ClientOtherErrors

**PercentSuccess** metrisk henter procentdelen af handlinger, som blev gennemført, baseret på deres HTTP-statuskode. Handlinger med status-koder af 2XX tæller som lykkes, mens handlinger med status-koder i 3XX, 4XX og 5XX områder, tælles som mislykkes og sænke **PercentSucess** metrikværdi. Disse handlinger registreres i logfilerne serversiden lagerplads med statussen transaktion **ClientOtherErrors**.

Det er vigtigt at være opmærksom på, at disse handlinger er fuldført, og derfor påvirker ikke andre målepunkter som tilgængelighed. Nogle eksempler på Handlinger, der blev udført korrekt, men, der kan medføre mislykkes HTTP statuskoder omfatter:
- **ResourceNotFound** (Ikke fundet 404), for eksempel fra en GET-anmodning til en blob, der ikke findes.
- **ResouceAlreadyExists** (Konflikt 409), for eksempel fra en **CreateIfNotExist** operation, hvor ressourcen findes allerede.
- **ConditionNotMet** (Ikke ændret 304), for eksempel fra en betinget operation som når en klient sender en **ETag** værdi og en HTTP **If-None-Match** header til at anmode om et billede, kun, hvis det er blevet opdateret siden den seneste handling.

Du kan finde en liste over almindelige REST-API fejlkoder, der returnerer lagerplads tjenesterne på siden <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Almindelige RESTEN API fejlkoder</a>.

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Kapacitet målepunkter vise en uventede stigning i kapacitet lagerforbrug


Hvis du får vist pludselig, uventede ændringer i kapacitet brugen i din lagerplads-konto, du kan undersøge årsagen ved først at se på din tilgængelighed målepunkter; for eksempel en stigning i antallet af mislykkede delete anmodninger kan føre til et større inden for den blob-lager, du bruger som programmet oprydning i bestemte handlinger du muligvis har forventede at være frigøre plads ikke fungerer muligvis som forventet (f.eks, fordi de SAS tokens, der bruges til at frigøre plads har udløbet).

### <a name="you-are-experiencing-unexpected-reboots"></a>Du oplever uventede genstarter af virtuelle Azure-computere, der har et stort antal vedhæftede virtuelle harddiske

Hvis Azure Virtual Machine (VM) indeholder et stort antal vedhæftet virtuelle harddiske, der findes i den samme lagerplads-konto, kan du overskrider skalerbarhed mål for en enkelt lagerplads konto medfører VM mislykkes. Du skal kontrollere minut målene for kontoen lagerplads (**TotalRequests**/**TotalIngress**/**TotalEgress**) til spidser, der overskrider skalerbarhed mål for en lagerplads konto. Se afsnittet "[målepunkter vise et større PercentThrottlingError]" for at få hjælp ved fastlæggelse af Hvis (throttling) opstod på kontoen lagerplads.

Hver enkelt input eller output handling på en virtuel harddisk fra en virtuel maskine oversætter Generelt **Få side** eller **Sætte side** operationer i den underliggende side blob. Derfor kan du bruge de anslåede IOP'ER for dit miljø til finjustere hvor mange virtuelle harddiske du kan have på en enkelt lagerplads konto baseret på bestemte funktionsmåden for dit program. Vi anbefaler, at du ikke har mere end 40 diske på en enkelt lagerplads konto. Se <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Azure lagerplads skalerbarhed og ydeevne destinationer</a> for oplysninger om de aktuelle skalerbarhed mål for lagerplads konti, især de samlede anmodning rente og samlede båndbredde til typen lagerplads konto du bruger.
Hvis du overskrider skalerbarhed målene for kontoen lagerplads, skal du markere dit virtuelle harddiske i flere forskellige lagerplads konti, for at reducere aktiviteten på hver enkelt konto.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Dit problem opstår i at bruge lagerplads emulatoren til udvikling eller test

Du typisk brug lagerplads emulatoren under udvikling og teste for at undgå kravet om en Azure-lager-konto. De almindelige problemer, der kan opstå, når du bruger lagerplads emulatoren er:

- [Funktionen "X" virker ikke i lagerplads emulatoren]
- [Fejlen "værdien af en HTTP-overskrifterne ikke er i det korrekte format" Når du bruger lagerplads emulatoren]
- [Kører lagerplads emulatoren kræver administratorrettigheder]

#### <a name="feature-X-is-not-working"></a>Funktionen "X" virker ikke i lagerplads emulatoren

Lagerplads emulatoren understøtter ikke alle funktioner i Azure lagerplads tjenester som tjenesten fil. Yderligere oplysninger finder du se <a href="http://msdn.microsoft.com/library/azure/gg433135.aspx" target="_blank">forskelle mellem feltet lagerplads Emulator og Azure lagerplads Services</a> på MSDN.

For disse funktioner, som lager emulatoren ikke understøtter, at bruge tjenesten Azure storage i skyen.

#### <a name="error-HTTP-header-not-correct-format"></a>Fejlen "værdien af en HTTP-overskrifterne ikke er i det korrekte format" Når du bruger lagerplads emulatoren

Du er ved at teste dit program, der bruger biblioteket lagerplads klient mod lokale lager emulatoren og metode kalder som **CreateIfNotExists** mislykkes fejlmeddelelsen "værdi for et af HTTP-overskrifterne ikke er det korrekte format". Dette angiver, at versionen af lagerplads emulatoren du bruger ikke understøtter versionen af biblioteket lagerplads klient, du bruger. Biblioteket lagerplads klient føjer sidehoved **x-ms-version** til alle anmodninger, der foretages. Hvis lagerplads emulatoren ikke genkender værdien i overskriften **x-ms-version** , afviser anmodningen.

Du kan bruge loggene lagerplads bibliotek klienten til at se værdien af **x-ms-version sidehoved** den bruges til at sende. Du kan også se værdien af **x-ms-version sidehoved** , hvis du bruger Fiddler til at spore anmodningerne fra klientprogrammet.

Dette scenarie opstår typisk, hvis du installerer og bruger den nyeste version af biblioteket lagerplads klienten uden at opdatere emulatoren lagerplads. Du skal installere den nyeste version af lagerplads emulatoren eller bruge skylagring i stedet for emulatoren for udvikling og test.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Kører lagerplads emulatoren kræver administratorrettigheder

Du bliver bedt om legitimationsoplysninger for administrator, når du kører emulatoren lagerplads. Dette sker kun, når du initialisering af lagerplads emulatoren for første gang. Når du har initialiseret emulatoren lagerplads, skal du ikke administrative rettigheder til at køre den igen.

Yderligere oplysninger finder du se <a href="http://msdn.microsoft.com/library/azure/gg433132.aspx" target="_blank">initialiseret lagerplads emulatoren ved hjælp af værktøjet kommandolinjeparametre</a> på MSDN (du kan også initialiseret lagerplads emulatoren i Visual Studio, som også kræver administratorrettigheder).

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Du oplever problemer med at installere Azure SDK til .NET

Når du forsøger at installere SDK, kan den ikke forsøger at installere emulatoren lagerplads på din lokale computer. Installation loggen indeholder et af følgende meddelelser:

- CAQuietExec: Fejl: kan ikke få adgang til SQL-forekomst
- CAQuietExec: Fejl: kan ikke oprette database

Det skyldes et problem med eksisterende LocalDB installation. Som standard bruger lagerplads emulatoren LocalDB bevares data, når den simulerer Azure lagerplads tjenesterne. Du kan nulstille din LocalDB forekomst ved at køre følgende kommandoer i et kommandopromptvindue før du forsøger at installere SDK.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

Kommandoen **Slet** fjerner alle gamle databasefiler fra tidligere installationer af emulatoren lagerplads.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Du har et andet problem med en lagringstjeneste

Hvis de foregående fejlfinding afsnit, ikke du skal medtage det problem, du har med en lagringstjeneste, bør du træffe følgende fremgangsmåde for diagnosticering og fejlfinding af problemet.

- Se din målepunkter at se, om der er nogen ændringer i din forventede grundlinjen funktionsmåde. Fra statistik, kan du muligvis afgøre, om problemet er midlertidig eller permanent, og hvilke lagerplads operationer problemet påvirker.
- Du kan bruge oplysningerne målepunkter kan hjælpe dig med at søge i dataene serversiden log mere detaljerede oplysninger om eventuelle fejl, der er til virkelighed. Disse oplysninger kan hjælpe dig med at fejlfinde og løse problemet.
- Hvis oplysningerne i loggene på serversiden ikke er tilstrækkelige til at foretage fejlfinding af problemet korrekt, kan du bruge loggene på klientsiden lagerplads klientbibliotek for at undersøge funktionsmåden for din klientprogrammet og værktøjer som Fiddler, Wireshark og Microsoft meddelelse Analyzer for at undersøge dit netværk.

Finde flere oplysninger om brug af Fiddler "[tillæg 1: Brug af Fiddler til at registrere HTTP og HTTPS trafik]."

Finde flere oplysninger om brug af Wireshark "[tillæg 2: Brug af Wireshark til at registrere netværkstrafik]."

Finde flere oplysninger om brug af Microsoft meddelelse Analyzer "[tillæg 3: Brug af Microsoft Message Analyzer til at registrere netværkstrafik]."

## <a name="appendices"></a>Tillæg

Listerne beskriver flere værktøjer, som du kan være nyttigt, når du er diagnosticering og fejlfinding af problemer med Azure-lager (og andre tjenester). Disse funktioner er ikke en del af Azure-lager, og nogle er tredjeparts produkter. Som sådan de værktøjer, der er beskrevet i disse tillæg er ikke omfattet af enhver support-aftale, har du måske med Microsoft Azure eller Azure-lager, og derfor som en del af din evalueringen skal du undersøge licens- og tilgængelige indstillinger fra udbydere af disse værktøjer.

### <a name="appendix-1"></a>Tillæg 1: Brug af Fiddler til at registrere HTTP og HTTPS trafik

Fiddler er et nyttigt værktøj til analyse af HTTP og HTTPS trafik mellem klientprogrammet og Azure-lagringstjeneste, du bruger. Du kan hente Fiddler fra <a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>.

> [AZURE.NOTE] Fiddler kan afkode HTTPS-trafikken; Du bør læse Fiddler dokumentationen grundigt at forstå, hvordan den gør dette, og at forstå konsekvenserne sikkerhed.

Dette tillæg indeholder en kort gennemgang af, hvordan du konfigurerer Fiddler at registrere trafik mellem den lokale computer, hvor du har installeret Fiddler og tjenesterne, Azure-lager.

Når du har startet Fiddler, begynder indsamle HTTP og HTTPS trafik på din lokale computer. Følgende er nogle nyttige kommandoer for at styre Fiddler:

- Stoppe og begynde at registrere trafik. Gå til **filer** i hovedmenuen, og klik derefter på **Registrere trafik** for at slå indsamle til og fra.
- Gem hentede trafikdata. På hovedmenuen skal du gå til **filen**, skal du klikke på **Gem**, og klik derefter på **Alle sessioner**: Dette gør det muligt at gemme trafikken i en Session arkivfilen. Du kan Genindlæs en Session arkiv senere til analyse, eller du kan sende den, hvis du ønsker til Microsoft support.

Hvis du vil begrænse mængden trafik, Fiddler passer, kan du bruge filtre, som du konfigurere under fanen **filtre** . Følgende skærmbillede viser et filter, der registrerer kun trafik, der sendes til **contosoemaildist.table.core.windows.net** lagerplads slutpunktet:

![][5]

### <a name="appendix-2"></a>Tillæg 2: Brug af Wireshark til at registrere netværkstrafik

Wireshark er et netværk protocol analyzer, der gør det muligt at få vist detaljerede pakkeoplysninger til en bred vifte af netværksprotokoller. Du kan hente Wireshark fra <a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>.

Følgende procedure viser, hvordan til at registrere detaljerede pakkeoplysninger for trafik fra den lokale computer hvor du har installeret Wireshark til tabeltjenesten i kontoen Azure-lager.

1.  Start Wireshark til din lokale computer.
2.  Vælg den lokale netværksgrænseflade eller grænseflader, der er tilsluttet internettet i sektionen **starte** .
3.  Klik på **Hent indstillinger**.
4.  Føje et filter til tekstfeltet **Filteret til hentning** . For eksempel konfigurerer **host contosoemaildist.table.core.windows.net** Wireshark at registrere kun pakker, som sendes til eller fra tabellen tjenesteslutpunkt i kontoen **contosoemaildist** lagerplads. Du kan finde en komplet liste over registrere filtre <a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>.

    ![][6]

5.  Klik på **Start**. Wireshark skal nu hente alle pakkerne send til eller fra tabellen tjenesteslutpunkt, som du bruger klientprogrammet på din lokale computer.
6.  Når du er færdig, hovedmenuen på **registrere** , og klik derefter **Stop**.
7.  Klik på **filer** og derefter på **Gem**for at gemme de hentede data i en Wireshark registrere fil, i hovedmenuen.

WireShark fremhæver eventuelle fejl, der findes i vinduet **packetlist** . Du kan også bruge vinduet **Ekspert oplysninger** (Klik på **analysér**, og klik derefter **Ekspert oplysninger**) til at få vist en oversigt over fejl og advarsler.

![][7]

Du kan også vælge at få vist TCP-dataene, som programlag ser den ved at højreklikke på TCP-data og vælge **Følg TCP-Stream**. Dette er især nyttig, hvis du har fået din gemte uden et filter til hentning. Se <a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">her</a> for at få flere oplysninger.

![][8]

> [AZURE.NOTE] Du kan finde flere oplysninger om brug af Wireshark <a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">Brugervejledning til Wireshark</a>.

### <a name="appendix-3"></a>Tillæg 3: Brug af Microsoft meddelelse Analyzer til at registrere netværkstrafik

Du kan bruge Microsoft meddelelse Analyzer til at registrere HTTP og HTTPS trafik på samme måde at Fiddler og registrere netværkstrafik på samme måde at Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurere en session med web sporing ved hjælp af Microsoft meddelelse Analyzer

Køre programmet Microsoft meddelelse Analyzer for at konfigurere en web sporingssession til HTTP og HTTPS trafik ved hjælp af Microsoft meddelelse Analyzer, og klik derefter på **Capture/sporing**i menuen **filer** . Vælg **Webtjenesteproxy**på listen over tilgængelige sporing scenarier. Tilføj derefter navnene på lagerplads slutpunkterne (du kan søge efter disse navne i portalen Azure klassisk) i panelet **Sporing scenarie konfiguration** i tekstfeltet **HostnameFilter** . Hvis navnet på din konto, Azure-lager er **contosodata**, skal du tilføje følgende til tekstfeltet **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] Et mellemrumstegn adskiller værtsnavne.

Når du er klar til at starte dataindsamling sporing, skal du klikke på knappen **Start med** .

Du kan finde flere oplysninger om Microsoft meddelelse Analyzer **Webtjenesteproxy** sporing, <a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">PEF WebProxy udbyder</a> findes på TechNet.

Indbyggede **Webtjenesteproxy** sporing i Microsoft meddelelse Analyzer er baseret på Fiddler; Det kan tage klientsiden HTTPS trafik og få vist ikke-krypterede HTTPS-meddelelser. **Webtjenesteproxy** sporing fungerer ved at konfigurere en lokal proxy for alle HTTP og HTTPS trafik, der giver det adgang til ikke-krypterede meddelelser.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticere netværksproblemer ved hjælp af Microsoft meddelelse Analyzer

Ud over at bruge Microsoft meddelelse Analyzer **Web Proxy** sporingen til at registrere oplysninger om HTTP/HTTPs-trafik mellem klientprogrammet og tjenesten storage, kan du også bruge indbyggede **Lokale Link Layer** sporingen til at registrere oplysninger om netværket pakke. I denne kan du kopiere data, der svarer til den pågældende, som du kan registrere med Wireshark og diagnosticere hjemmenetværk problemer såsom mistede pakker.

Følgende skærmbillede viser et eksempel **Lokale Link Layer** sporing med nogle **informativ** meddelelser i kolonnen **DiagnosisTypes** . At klikke på et ikon i kolonnen **DiagnosisTypes** viser oplysninger om meddelelsen. I dette eksempel sendt serveren, igen meddelelse #305, fordi det ikke har modtaget en bekræftelse fra klienten på computeren:

![][9]

Når du opretter sporing sessionen i Microsoft meddelelse Analyzer, kan du angive filtre for at reducere mængden af støj i sporet. Klik på linket **Konfigurer** ud for **Microsoft-Windows-NDIS-PacketCapture**på **registrere / spor** siden hvor du definerer sporingen. Følgende skærmbillede viser en konfiguration, der filtrerer TCP-trafik til tre lagerplads tjenester IP-adresser:

![][10]

Du kan finde flere oplysninger om Microsoft meddelelse Analyzer lokale Link Layer sporingen, <a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">PEF-NDIS-PacketCapture udbyder</a> findes på TechNet.

### <a name="appendix-4"></a>Tillæg 4: Brug af Excel til at få vist målepunkter og logge af data

Mange værktøjer, der gør det muligt at hente Lagringsmål-data fra Azure-tabellager i en afgrænset format, der gør det nemt at indlæse dataene i Excel til gennemsyn og analyse. Lagerplads logføring data fra Azure blob-lager er allerede i et afgrænset format, som du kan indlæse i Excel. Men, skal du føje relevante kolonneoverskrifter, der er baseret på oplysninger på <a href="http://msdn.microsoft.com/library/azure/hh343259.aspx" target="_blank">Lagerplads Analytics Log Format</a> og <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Lagerplads Analytics målepunkter tabelskema</a>.

At importere dataene lagerplads logføring til Excel, når du hente det fra blob-lager:

- Klik på menuen **Data** **Fra tekst**.
- Gå til den logfil, du vil have vist, og klik på **Importér**.
- Vælg **afgrænset**på trin 1 i **Guiden Tekstimport**.

På trin 1 i **Guiden Tekstimport**, Vælg **semikolon** som kun afgrænser, og vælg dobbelt-quote som **tekst operator**. Klik på **Udfør** , og vælge, hvor du vil placere dataene i din projektmappe.

### <a name="appendix-5"></a>Tillæg 5: Overvågning med programmet indsigt til Visual Studio Team Services

Du kan også bruge funktionen programmet indsigt til Visual Studio Team Services som en del af ydeevnen og tilgængeligheden af overvågning. Dette værktøj kan:

- Kontrollér, at din webtjeneste er tilgængelig og svarede. Om din app er et websted eller en enhed-app, der bruger en webtjeneste, kan det test din URL-adresse hvert par fra placeringer over hele verden, og fortæller dig, hvis der opstår et problem.
- Diagnosticere hurtigt eventuelle problemer med ydeevnen eller undtagelser i din webtjeneste. Finde ud af, hvis den er der strækkes CPU eller andre ressourcer, kan du få staksporinger fra undtagelser, og nemt at gennemsøge log sporinger. Hvis den app ydeevnen falder under acceptable grænser, kan vi sender dig en mail. Du kan overvåge både .NET og Java webtjenester.

I klokkeslættet for oprettelsen af programmet indsigt findes i Vis udskrift. Du kan finde flere oplysninger i <a href="http://msdn.microsoft.com/library/azure/dn481095.aspx" target="_blank">Programmet indsigt til Visual Studio Team Services på MSDN</a>.


<!--Anchors-->
[Introduktion]: #introduction
[Hvordan denne vejledning er organiseret]: #how-this-guide-is-organized

[Overvågning af dine lagringstjeneste]: #monitoring-your-storage-service
[Overvågning af tjenestetilstand]: #monitoring-service-health
[Overvågning kapacitet]: #monitoring-capacity
[Overvågning tilgængelighed]: #monitoring-availability
[Overvågning af ydeevnen]: #monitoring-performance

[Diagnosticere lagerplads problemer]: #diagnosing-storage-issues
[Service sundhed problemer]: #service-health-issues
[Problemer med ydeevnen]: #performance-issues
[Diagnosticere fejl]: #diagnosing-errors
[Lagerplads emulator problemer]: #storage-emulator-issues
[Lagerplads logføring værktøjer]: #storage-logging-tools
[Ved hjælp af netværk logføring værktøjer]: #using-network-logging-tools

[Sporing af til slut]: #end-to-end-tracing
[Korrelerende logdata]: #correlating-log-data
[Klient-ID anmodning]: #client-request-id
[Server anmodnings-ID]: #server-request-id
[Tidsstempler]: #timestamps

[Vejledning til fejlfinding]: #troubleshooting-guidance
[Målepunkter vise høj AverageE2ELatency og lav AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Målepunkter vise lav AverageE2ELatency og lav AverageServerLatency, men klienten oplever lang ventetid]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Målepunkter Vis høj AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Du oplever uventede forsinkelser i levering af meddelelser på en kø]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Målepunkter vise en stigning i PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Midlertidige stigning i PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanent stigning i PercentThrottlingError fejl]: #permanent-increase-in-PercentThrottlingError
[Målepunkter vise en stigning i PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Målepunkter vise en stigning i PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klienten modtager HTTP 403 (forbudt) meddelelser]: #the-client-is-receiving-403-messages
[Klienten modtager HTTP 404 (blev ikke fundet) meddelelser]: #the-client-is-receiving-404-messages
[Klienten eller en anden proces slettet objektet]: #client-previously-deleted-the-object
[Et delt Access signatur (SAS) godkendelse problem]: #SAS-authorization-issue
[Klientsiden JavaScript-kode har ikke tilladelse til at få adgang til objektet]: #JavaScript-code-does-not-have-permission
[Netværk mislykkedes]: #network-failure
[Klienten modtager HTTP 409 (konflikt) meddelelser]: #the-client-is-receiving-409-messages

[Målepunkter vise lav PercentSuccess eller analytics logposter have handlinger med transaktionsstatus af ClientOtherErrors]: #metrics-show-low-percent-success
[Kapacitet målepunkter vise en uventede stigning i kapacitet lagerforbrug]: #capacity-metrics-show-an-unexpected-increase
[Du oplever uventede genstarter af virtuelle maskiner, der har et stort antal vedhæftede virtuelle harddiske]: #you-are-experiencing-unexpected-reboots
[Dit problem opstår i at bruge lagerplads emulatoren til udvikling eller test]: #your-issue-arises-from-using-the-storage-emulator
[Funktionen "X" virker ikke i lagerplads emulatoren]: #feature-X-is-not-working
[Fejlen "værdien af en HTTP-overskrifterne ikke er i det korrekte format" Når du bruger lagerplads emulatoren]: #error-HTTP-header-not-correct-format
[Kører lagerplads emulatoren kræver administratorrettigheder]: #storage-emulator-requires-administrative-privileges
[Du oplever problemer med at installere Azure SDK til .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Du har et andet problem med en lagringstjeneste]: #you-have-a-different-issue-with-a-storage-service

[Tillæg]: #appendices
[Tillæg 1: Brug af Fiddler til at registrere HTTP og HTTPS trafik]: #appendix-1
[Tillæg 2: Brug af Wireshark til at registrere netværkstrafik]: #appendix-2
[Tillæg 3: Brug af Microsoft meddelelse Analyzer til at registrere netværkstrafik]: #appendix-3
[Tillæg 4: Brug af Excel til at få vist målepunkter og logge af data]: #appendix-4
[Tillæg 5: Overvågning med programmet indsigt til Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/overview.png
[2]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/portal-screenshot.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-2.png
