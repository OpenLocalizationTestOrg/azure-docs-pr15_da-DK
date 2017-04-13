<properties
   pageTitle="Høj tilgængelighed til Azure programmer | Microsoft Azure"
   description="Teknisk oversigt og detaljerede oplysninger om at designe og opbygge programmer til høj tilgængelighed på Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Høj tilgængelighed for bygget på Microsoft Azure-programmer

En meget tilgængeligt program absorberer udsving i tilgængelighed, Indlæs og midlertidige fejl i den afhængige tjenester og hardware. Programmet fortsætter med at fungere på en acceptabel bruger og systematiske svar plan, som defineret af virksomhedens behov eller program service niveau aftaler (SLA).

##<a name="azure-high-availability-features"></a>Azure høj tilgængelighed funktioner

Azure indeholder mange indbyggede platformfunktioner, der understøtter meget tilgængelige programmer. I dette afsnit beskrives nogle af de vigtigste funktioner. Du kan finde en mere omfattende analyse af platformen, [Azure fleksibilitet teknisk vejledning](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Strukturen controller

Azure-strukturen controller bestemmelser og overvåger betingelsen af Azure Beregn forekomster. Strukturen controller kontrollerer status for hardwaren og software i host og Gæst maskine forekomster. Når programmet registrerer en fejl, håndhæves SLA ved at flytte automatisk VM forekomster. Begrebet yderligere fejl og opgradering af domæner understøtter Beregn SERVICENIVEAUAFTALE.

Når flere forekomster af skytjeneste rolle er installeret, installerer Azure disse forekomster til forskellige fejl domæner. Et domæne rammen er som en anden hardware rack i samme område. Fejl domæner reducere sandsynligheden for, at en oversatte hardwarefejl vil afbryde til tjenesten for et program. Du kan ikke administrere antallet af fejl domæner, der er tildelt din kollega eller web roller. Strukturen controller anvender dedikeret ressourcer, der er adskilt fra Azure-hostet programmer. Der er 100 procent oppetid, fordi den fungerer som kerne Azure-systemet. Det overvåger og administrerer rolle forekomster på tværs af et domæner.

I følgende diagram vises Azure delte ressourcer, strukturen controller udruller og administrerer på tværs af forskellige fejl domæner.

![Forenklet visning af isolation af et domæne](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Opgradering domæner ligner et domæner i funktionen, men de understøtter opgraderinger i stedet for fejl. En opgradering domæne er en logisk måleenhed forekomst adskillelsen, der bestemmer, hvilke forekomster i en bestemt tjeneste opgraderes på et sted i gang. Som standard til implementeringen af denne hostet service defineres fem opgradering domæner. Du kan dog ændre denne værdi i definitionsfil tjeneste. Antag for eksempel, at du har otte forekomster af din web rolle. Der vil være to forekomster i tre opgradering domæner og to forekomster i ét opgradering domæne. Azure definerer opdatering rækkefølgen, men den er baseret på antallet opgradering domæner. Se [opdatere en skybaseret tjeneste](../cloud-services/cloud-services-update-azure-service.md), kan finde flere oplysninger om opgradering domæner.

###<a name="features-in-other-services"></a>Funktioner i andre tjenester

Ud over funktionerne platform, der understøtter høj Beregn tilgængelighed integrerer Azure høj tilgængelighed funktioner i dets andre tjenester. For eksempel fører Azure-lager tre kopier af alle blob, tabel og kø data. Gør det også mulighed for geografisk gentagelse til lagring af sikkerhedskopier af BLOB og tabeller i en sekundær område. Azure indhold levering netværk kan BLOB cachelagres over hele verden til både redundans og skalerbarhed. Azure SQL-Database fører samt flere replikaer.

Se [Bedste fremgangsmåder til Design af stor skala tjenesterne på Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) papiret ud over [fleksibilitet teknisk vejledning](https://aka.ms/bctechguide) række artikler. Disse rapporter giver en dybere diskussion af Azure-platformen tilgængelighed af funktioner.

Selvom Azure indeholder flere funktioner, der understøtter høj tilgængelighed, er det vigtigt at forstå deres begrænsninger:

- For Beregn garanterer Azure, at dine roller er tilgængelig og kører, men det ikke, om dit program er kører eller overloadede.
- Til Azure SQL-Database replikeres data synkront i området. Du kan vælge aktive geografisk replikering, hvilket gør det muligt for op til fire yderligere database Kopier i samme område (eller forskellige områder). Disse replikaer er ikke punkt-in-time sikkerhedskopier. SQL-databaser, leverer punkt-in-time ekstra funktioner. Hvis du vil vide mere om SQL-Database punkt-in-time-funktioner, du læse [Azure SQL Database punkt i tid Gendan](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Tabel og blob data replikeres til Azure-lager, som standard til et andet område. Du åbne ikke replikaerne, indtil Microsoft vælger at skifte over til det andet websted. Område serverskift kun i forbindelse med en længere område hele tjenesteforstyrrelse, og der er ingen SLA geografisk failover gang. Det er også vigtigt at være opmærksom på, at en hvilken som helst databeskadigelse hurtigt opslag til replikaerne.

Disse årsager, skal du tillæg platform tilgængelighed af funktioner med tilgængeligheden af programmet-specifikke funktioner. Tilgængeligheden af programmet-specifikke funktioner omfatter funktionen blob snapshot for at oprette punkt-in-time sikkerhedskopier af blob-data.

###<a name="availability-sets-for-azure-virtual-machines"></a>Tilgængelighed angiver for virtuelle Azure-computere

Størstedelen af denne artikel fokuserer på cloud services, som bruger en platform som en tjeneste (PaaS)-model. Der er dog også tilgængeligheden af bestemte funktioner for virtuelle Azure-computere, som bruger en infrastruktur som en tjeneste (IaaS)-model. For at opnå høj tilgængelighed med virtuelle maskiner, skal du bruge tilgængelighed sæt. Angiv en tilgængelighed fungerer en lignende funktion til fejl og opgradering af domæner. Inden for en gruppe af tilgængelighed kan placerer Azure virtuelle maskiner på en måde, der forhindrer oversatte hardware-fejl og vedligeholdelsesaktiviteter i hvilket ned alle maskiner i den pågældende gruppe. Tilgængelighed sæt kræves for at opnå Azure SERVICENIVEAUAFTALE for tilgængeligheden af virtuelle maskiner.

I det følgende diagram indeholder en repræsentation af to tilgængelighed sæt, gruppen web og SQL Server virtuelle maskiner, henholdsvis.

![Tilgængelighed angiver for virtuelle Azure-computere](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] I det foregående diagram er SQL Server installeret og køre på virtuelle maskiner. Dette er forskellig fra den forrige diskussion af Azure SQL-Database, som indeholder en database som en administreret tjeneste.

##<a name="application-strategies-for-high-availability"></a>Programmet strategier for høj tilgængelighed

De fleste programmet strategier for høj tilgængelighed involverer redundans eller fjernelse af hårde afhængigheder mellem programkomponenter. Programmet design skal understøtte fejltolerance under sporadisk nedetid af Azure eller tredjepartstjenester. I følgende afsnit beskrives programmet mønstre for at øge tilgængeligheden af din skytjenester.

###<a name="asynchronous-communication-and-durable-queues"></a>Asynkron kommunikation og robust køer

Overvej asynkron kommunikation mellem løst sammenkoblede tjenester at øge tilgængelighed i Azure-programmer. Skrive meddelelser til lagerplads køer eller Azure Service Bus køer til at behandle senere i dette mønster. Når du skriver meddelelsen til køen, returnerer kontrolelement med det samme til afsenderen af meddelelsen. Et andet niveau af programmet håndterer meddelelsen behandling, typisk implementeret som en kollega rolle. Hvis rollen arbejder går ned, samle meddelelser i køen, indtil behandling tjenesten er gendannet. Så længe køen er tilgængelige, er der ingen direkte afhængighed mellem front end afsenderen og meddelelsesprocessor. Dette fjerner kravet om synkron serviceopkald, der kan være en overførselshastighed flaskehals i distribuerede programmer.

En variation af denne bruger Azure-lager (BLOB, tabeller, køer) eller Service Bus køer som failover placering for mislykkedes database opkald. For eksempel en synkron opkald i et program til en anden tjeneste (såsom Azure SQL-Database) kan ikke flere gange. Du kan muligvis sekventielt disse data til robust lagerplads. På et senere tidspunkt, når den tjeneste eller database er tilbage online, kan programmet igen sende anmodningen fra lagerplads. Forskellen i denne model er, at den mellemliggende placering ikke er en konstant del af arbejdsprocessen for programmet på computeren. Det bruges kun i situationer.

Forhindre en downed back end-tjeneste i hvilket ned hele programmet i både scenarier, asynkron kommunikation og mellemliggende lagerplads. Køer fungere som et logisk mellemled. Se [Azure køer og Azure Service Bus køer – sammenlignet og i farvekategorier](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)have mere at vide om valg af den korrekte kø service.

###<a name="fault-detection-and-retry-logic"></a>Fejl registrering, og prøv igen logik

En vigtig ting i meget tilgængeligt programdesign er at bruge forsøg kode til problemfrit for at håndtere en tjeneste, som er midlertidigt nede. [Midlertidige fejl håndtering af programmet blok](https://msdn.microsoft.com/library/hh680934.aspx), udviklet af Microsoft Patterns og fremgangsmåder teamet, hjælper udviklere med denne proces. Ordet "forbigående" betyder, at en midlertidig tilstand, der varer kun i relativt kort tid. I forbindelse med denne artikel er en del af udvikle et program, der er meget tilgængelige håndtering af midlertidige mislykkede forsøg. Eksempler på midlertidige betingelser omfatter forbigående netværksfejl og tabte databaseforbindelser.

Midlertidige fejl håndtering af programmet blokering er en nem metode til at håndtere fejl i din kode på en korrekt måde. Du kan bruge det til at forbedre tilgængeligheden af dine programmer ved at tilføje robust midlertidige fejl fejlhåndtering logik. I de fleste tilfælde forsøg håndterer kort blev afbrudt og tilslutter afsenderen og modtageren efter en eller flere mislykkede forsøg. En vellykket forsøg på at typisk går sig til brugere af programmet.

Udviklere har tre muligheder for at administrere deres forsøg: stigende, fast interval, og eksponentielle. Trinvis venter længere før hver prøv igen i en stigende lineær måde (for eksempel 1, 2, 3 og 4 sekunder). Fast interval venter det samme antal timer mellem hvert forsøg (for eksempel 2 sekunder). For en mere tilfældig indstilling venter eksponentiel back-fra længere mellem nye forsøg. Men bruger eksponentiel funktionsmåde (for eksempel 2, 4, 8 og 16 sekunder).

Overordnet strategi i din kode er:

1. Definere din strategi for prøv igen og politik.
1. Prøv den handling, der kan medføre en midlertidig fejl.
1. Hvis midlertidige fejl opstår, skal du aktivere politikken prøv igen.
1. Hvis alle forsøg ikke, kan du løse en endelige undtagelse.

Teste din forsøg i simuleret mislykkede forsøg på at sikre, at nye forsøg på efterfølgende operationer ikke medfører en uventede længerevarende forsinkelse. Gør dette, inden du beslutter, mislykkes den overordnede opgave.

###<a name="reference-data-pattern-for-high-availability"></a>Reference datamønster for høj tilgængelighed

Referencedata er skrivebeskyttede data fra et program. Disse data indeholder den business kontekst, som opretter programmet transaktions data i løbet af en business-handling. Transaktions data er en punkt-in-time funktion reference data. Derfor afhænger dens integritet af snapshot af referencedataene på tidspunktet for posteringen. Dette er en smule Løs definition, men det skulle være nok til vores formål her.

Referencedata i forbindelse med et program er nødvendigt for programmet kan fungere. De respektive programmer opretter og vedligeholder referencedata. master data (MDM) systemer udføre ofte denne funktion. Disse systemer er ansvarlig for livscyklussen for referencedataene. Eksempler på referencedata omfatter produktkatalog, medarbejder master, dele master og udstyr master. Referencedata kan også stammer fra uden for organisationen, for eksempel postnumre eller skat satser. Strategier for at øge tilgængeligheden af referencedata er typisk mindre svært end rettighederne for transaktions data. Referencedata har fordelen ved at hovedsageligt fast.

Du kan foretage Azure internettet og arbejder roller, der forbruger referencedata selvstændige på kørselstidspunktet ved at udrulle referencedata sammen med programmet. Hvis størrelsen på det lokale lager tillader en sådan installation, er dette en ideel tilstand. Integreret databaser (SQL, NoSQL) eller XML-filer, der er installeret på et lokalt filsystem hjælper med af Azure Beregn skalaenheder uafhængighed. Men, bør du have en metode til at opdatere data i hver rolle uden geninstallation. Placer eventuelle opdateringer til referencedataene til et cloud storage slutpunkt (for eksempel Azure Blob-lager eller SQL-Database) for at gøre dette. Føj kode til hver rolle, som henter dataopdateringer til noderne Beregn ved rolle Start. Du kan også tilføje kode, der gør det muligt for en administrator til at udføre et tvunget download til rolle forekomster.

Du kan øge tilgængelighed ved bør rollerne, der også indeholde et sæt referencedata i tilfælde af lager er nede. Dette gør det muligt for rollerne, der skal starte med et grundlæggende sæt referencedata, indtil lagerplads ressourcen bliver tilgængelig for opdateringerne.

![Programmet høj tilgængelighed via selvstændige Beregn noder](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Én overvejelser for denne mønster er installation, og start hastighed for dine roller. Hvis du anvender eller hente store datamængder reference på Start, kan det øge mængden tid, det tager for at dreje nye installationer eller rolle forekomster. Det kan være en acceptabel fordeling for uafhængighed af opbevare de referencedata, der er umiddelbart tilgængelige på hver rolle i stedet for afhængigt af ekstern storage tjenester.

###<a name="transactional-data-pattern-for-high-availability"></a>Transaktions datamønster for høj tilgængelighed

Transaktions data er de data, som programmet genererer i en business kontekst. Transaktions data er en kombination af sæt af forretningsprocesser, der implementerer programmet og referencedata, der understøtter disse processer. Eksempler på vist rapportdata kan medtage ordrer, avancerede forsendelses meddelelser, fakturaer og kunde relation management (CRM) salgsmuligheder. Transaktions dataene dermed oprettes der indføres til eksterne systemer til registrering eller til videre behandling.

Husk på, der henviser kan ændre data i de systemer, der er ansvarlige for disse data. Derfor skal vist rapportdata gemme konteksten punkt-in-time reference data, så der er minimale eksterne afhængigheder til dens semantisk konsistens. For eksempel overveje fjernelse af et produkt, som kataloget nogle måneder efter en ordre er opfyldt. Den bedste fremgangsmåde er at integrere den ønskede reference datakontekst som muligt i posteringen. Dette bevarer den semantik, der er knyttet til posteringen, selvom referencedata ændres, når posteringen registreres.

Som tidligere nævnt udlåne arkitekturer, der bruger Løs kobling og asynkron kommunikation sig til et højere niveau af tilgængelighed. Dette gælder for transaktions data samt, men implementeringen er mere kompleks. Database for at garantere posteringen traditionelle transaktions ideer typisk skal have. Når du indsætter mellemliggende lag, skal programkoden korrekt håndtere dataene på forskellige lag, du vil sikre tilstrækkelige konsistens og holdbarhed.

Følgende sekvens beskrives en arbejdsproces, der adskiller hentning af transaktions data fra dens behandling:

1. Web beregningsnode: Præsenter refererer til data.
1. Ekstern storage: Gem mellemliggende transaktions data.
1. Web beregningsnode: fuldføre slutbrugeren transaktion.
1. Web beregningsnode: sende den færdige transaktions data sammen med konteksten reference data til midlertidigt robust lager, der er garanti for at give et mere forudsigelige svar.
1. Web beregningsnode: Signal slutbrugerlicensaftale fuldførelse af posteringen.
1. Baggrund beregne node: udtrække transaktions dataene, efter behandle den Hvis det er nødvendigt, og send den til den endelige lagerplads placering i det aktuelle system.

I det følgende diagram viser én mulige implementering af dette design i en Azure-hostet skybaseret tjeneste.

![Høj tilgængelighed via Løs kobling](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

På stiplet pil i det foregående diagram angiver asynkron behandling. Rollen front end-kender ikke til denne asynkron behandling. Fører til opbevaring af transaktion på den endelige destination med reference til det aktuelle system. På grund af ventetid, der introducerer denne asynkron model, findes transaktions dataene ikke umiddelbart for forespørgsel. Derfor hver enhed transaktions data skal gemmes i en cache, eller brug for en bruger sessioner lov til at opfylde øjeblikkelig Brugergrænsefladen.

Rollen web er selvstændige fra resten af infrastrukturen. Dens tilgængelighed profil er en kombination af rollen web og Azure køen og ikke hele infrastrukturen. Ud over høj tilgængelighed kan denne metode rollen web skalere vandret, uafhængigt af back end-lagerplads. Denne høj tilgængelighed model kan have indvirkning på økonomi-handlinger. Yderligere komponenter som Azure køer og arbejder roller kan påvirke månedlige brugen omkostninger.

Bemærk, at det forrige diagram viser én implementering af denne Afkoblet metode til transaktions data. Der findes mange andre mulige installationer. Følgende liste indeholder nogle alternativer:

 * En kollega rolle kan være placeret mellem rollen web og lagerplads køen.
 * En Service Bus kø kan bruges i stedet for en kø Azure-lager.
 * Den endelige destination kan være Azure-lager eller en anden database-udbyder.
 * Azure Cache kan bruges på web lag til at levere øjeblikkelig cachelagring kravene efter posteringen.

###<a name="scalability-patterns"></a>Skalerbarhed mønstre

Det er vigtigt at være opmærksom på, at skalerbarhed af skytjenesten direkte påvirker tilgængelighed. Hvis øgede belastning medfører, at din tjeneste ikke svarer, er bruger indtryk, at programmet er nede. Følg de bedste fremgangsmåder til skalerbarhed baseret på forventede programmet Indlæs og fremtidige forventninger. Højeste skalaen omfatter mange overvejelser, som brug af enkelt kontra flere lagerplads konti, deling på tværs af flere databaser og cachelagring strategier. Se [Bedste fremgangsmåder til Design af stor skala tjenesterne på Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)for et detaljeret indblik i disse mønstre.

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en række artikler fokuseret på [nedbrud og høj tilgængelighed for bygget på Microsoft Azure-programmer](./resiliency-disaster-recovery-high-availability-azure-applications.md). Næste artikel i denne serie er [nedbrud for bygget på Microsoft Azure-programmer](./resiliency-disaster-recovery-azure-applications.md).
