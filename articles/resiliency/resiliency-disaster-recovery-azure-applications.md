<properties
   pageTitle="Nedbrud til Azure-programmer | Microsoft Azure"
   description="Teknisk oversigt og detaljerede oplysninger om at designe programmer til nedbrud på Microsoft Azure."
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

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>Nedbrud for bygget på Microsoft Azure-programmer

Høj tilgængelighed er om administration af midlertidig fejl, er nedbrud (DR) om katastrofal tab af programmets funktionalitet. For eksempel gennemgå det scenario, hvor et område går ned. I dette tilfælde skal du have en for at køre programmet eller adgang til dine data uden for området Azure. Udførelse af denne plan omfatter personer, processer og understøttende programmer, der tillader systemet til funktionen. Business og teknologi ejerne, der definerer systemets funktionel tilstand for nedbrud bestemme også funktionalitet for tjenesten under nedbrud. Hvor mange funktioner kan tage et par formularer: helt utilgængelige, delvist til rådighed (nedsat funktionalitet, eller forsinket behandling), eller fuldt tilgængelig.

##<a name="azure-disaster-recovery-features"></a>Funktioner til Azure genoprettelse efter genoprettelse

Som med tilgængelighed overvejelser i forbindelse med, har Azure [fleksibilitet teknisk vejledning](./resiliency-technical-guidance.md) , som er udviklet til at understøtte nedbrud. Der er også en relation mellem nogle af funktionerne tilgængelighed af Azure og genoprettelse efter nedbrud. For eksempel øges for administration af roller på tværs af et domæner tilgængeligheden af et program. Uden pågældende management bliver en ikke-afviklet hardwarefejl et scenarie med "nedbrud". Så den korrekte anvendelse af tilgængelighed af funktioner og strategier er en vigtig del af nedbrud korrektur dit program. I denne artikel går dog ud over generelt tilgængelig problemer til mere alvorlige (og mere sjældent) nedbrud begivenheder.

##<a name="multiple-datacenter-regions"></a>Flere datacenter områder

Azure fører datacentre i mange områder i hele verden. Denne infrastruktur understøtter flere genoprettelse efter nedbrud scenarier, som leveres system geografisk-replikering Azure-lager til sekundær områder. Det betyder også, at du kan hurtigt og nemt vil installere en skybaseret tjeneste til flere placeringer over hele verden. Sammenlign det med omkostninger og besvær med at køre din egen datacentre i flere områder. Implementering af data og -tjenester til flere områder hjælper med at beskytte dit program fra overordnede udfald i et enkelt område.

##<a name="azure-traffic-manager"></a>Azure trafik Manager

Når en områdespecifik fejl opstår, skal du omdirigere trafik til services eller installationer i et andet område. Du kan gøre denne rute manuelt, men det er mere effektivt at bruge en automatiseret proces. Azure trafik Manager er udviklet til denne opgave. Du kan bruge det til automatisk for at styre sekundære bruger trafik til et andet område i tilfælde af det primære område mislykkes. Da trafik management er en vigtig del af den overordnede strategi, er det vigtigt at forstå de grundlæggende funktioner i trafik Manager.

I følgende diagram brugere, der opretter forbindelse til en URL-adresse, der er angivet for trafik overordnet (__http://myATMURL.trafficmanager.net__) og pågældende resume faktisk websted URL-adresserne (__http://app1URL.cloudapp.net__ og __http://app2URL.cloudapp.net__). Afhængigt af, hvordan du konfigurerer kriterierne for når til distribution brugere, sendes brugere til webstedet for korrekte faktisk når politikken bestemmer. Politikindstillingerne for er round robin-, ydeevnen eller failover. Af hensyn til denne artikel vil vi være pågældende med indstillingen failover.

![Routing via Azure trafik Manager](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Når du konfigurerer trafik Manager, kan du angive et nyt trafik Manager DNS præfiks. Dette er præfikset URL-adresse, du vil angiver, at dine brugere at få adgang til tjenesten. Trafik Manager abstracts nu et niveau op og ikke på regionsniveauet for justering af belastning. Trafik Manager DNS knyttes til en CNAME for alle de installationer, som det vedligeholder.

Inden for trafik Manager skal du angive prioriteten af de installationer, som brugerne sendes til, når der opstår fejl. Trafik Manager overvåger slutpunkterne på installationer og noter, når den primære installation mislykkes. På mislykket trafik Manager analyserer den prioriterede liste over installationer og omdirigerer brugere til den næste på listen.

Selvom trafik Manager bestemmer Sådan kommer du i en failover, kan du bestemme, om domænet failover er passive eller aktiv, mens du ikke er i failover tilstand. Denne funktionalitet har ingen betydning for med Azure trafik Manager. Trafik Manager registrerer en fejl i det primære websted og bevæges hen over til webstedet, failover. Trafik Manager bevæges hen over uanset om webstedet i øjeblikket er fungerer brugere, eller ej.

Du kan finde flere oplysninger om, hvordan Azure trafik Manager fungerer referere til:

 * [Oversigt over trafik Manager](../traffic-manager/traffic-manager-overview.md)
 * [Konfigurere failover routing metode](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Azure nedbrud scenarier

De følgende afsnit dækker flere forskellige typer nedbrud scenarier. Tjeneste i område hele afbrydelser er ikke kun årsagen til hele programmet mislykkede forsøg. Dårlig design eller administration fejl kan også medføre afbrydelser. Det er vigtigt at overveje de mulige årsager til en fejl under design og tester din gendannelse plan. En god plan udnytter Azure funktioner og augments dem med programmet-specifikke strategier. Det valgte svar, bestemmes af vigtigheden af programmet, gendannelse punkt formålet (frigivne Produktionsordre), og formålet gendannelse tid (RTO).

###<a name="application-failure"></a>Programfejl

Azure trafik Manager håndterer automatisk fejl, der stammer fra den underliggende hardware eller operativsystem software i virtuel værtsmaskinen. Azure opretter en ny forekomst af rollen på en server, der fungerer og føjer den til justering af belastning rotationen. Hvis antallet af forekomster af rolle er større end 1, forskydes Azure behandling til de andre kørende rolle forekomster mens erstatte noden mislykkedes.

Der findes alvorlige programfejl, der udføres uafhængigt af hardware eller operativsystem fejl. Programmet kan mislykkes på grund af katastrofal undtagelserne skyldes forkert logik eller problemer med dataintegritet. Du skal inkorporere nok telemetri i koden, så et system til overvågning kan registrere fejl betingelser og give besked administrator programmet på computeren. En administrator, der har fuldt kendskab til gendannelsesprocesser nedbrud kan gøre en beslutning om at kalde en failover proces. Alternativt kan en administrator blot acceptere en tilgængelighed afbrydelse for at løse alvorlige fejl.

###<a name="data-corruption"></a>Beskadigelse af data

Azure gemmer automatisk dine Azure SQL-Database og Azure-lager data tre gange give i forskellige fejl domæner i det samme område. Hvis du bruger geografisk gentagelse, gemmes dataene tre flere gange i et andet område. Men hvis brugerne eller dit program beskadiger disse data i den primære kopi, dataene hurtigt kopieres til de andre kopier. Desværre kan resulterer dette i tre kopier af beskadigede data.

Hvis du vil administrere potentielle beskadigelse af dine data, har du to muligheder. Først, kan du administrere en brugerdefineret strategi for sikkerhedskopiering. Du kan gemme dine sikkerhedskopier i Azure eller en lokal, afhængigt af din virksomhedens behov eller styring regler. En anden mulighed er at bruge den nye punkt-in-time gendannelsesindstilling for at gendanne en SQL-database. Yderligere oplysninger finder du i afsnittet på [data strategier for nedbrud](#data-strategies-for-disaster-recovery).

###<a name="network-outage"></a>Netværk afbrydelse

Når dele af Azure netværket er ikke tilgængelig, kan du ikke kunne få adgang til din programmet eller dine data. Hvis en eller flere forekomster af rolle er tilgængelige på grund af netværksproblemer, bruger Azure resterende tilgængelige forekomster af programmet. Hvis dit program ikke kan få adgang til dataene på grund af en Azure netværk afbrydelse, kan du potentielt køre i forringet tilstand lokalt ved hjælp af cachelagrede data. Du har brug at udvikle nedbrud gendannelse strategi for kører i forringet tilstand i programmet. For nogle programmer, kan det ikke være praktisk.

En anden mulighed er at gemme data i en anden placering, indtil forbindelsen er genoprettet. Hvis forringet tilstand ikke er en indstilling, er de resterende indstillinger nedetid for programmet eller failover til et andet område. Designet af et program, der kører i forringet tilstand er den ønskede en forretningsbeslutninger som en teknisk. Dette gennemgås yderligere i afsnittet om [nedsat programmets funktionalitet](#degraded-application-functionality).

###<a name="failure-of-a-dependent-service"></a>Fejl i forbindelse med en afhængige tjeneste

Azure indeholder mange tjenester, der kan opleve periodiske nedetid. Overvej [Azure Redis Cache](https://azure.microsoft.com/services/cache/) som et eksempel. Tjenesten med flere lejer indeholder cachelagring funktioner i dit program. Det er vigtigt at overveje, hvad sker der i dit program, hvis den afhængige tjeneste ikke er tilgængelig. Dette scenario er lig med dette netværk afbrydelse scenario på mange måder. Dog hensyn hver tjeneste uafhængigt resulterer i potentielle forbedringer til din overordnede plan.

Azure Redis Cache indeholder cachelagring i dit program fra i skyen implementeringen af denne service, som indeholder nedbrud gendannelse fordele. Tjenesten kører først nu på roller, der er lokale for din installation. Derfor er du bedre kan overvåge og administrere status for cachen som en del af din overordnede processer for styring for skytjenesten. Denne type cachelagring Fremviser også nye funktioner. En af de nye funktioner er høj tilgængelighed til cachelagrede data. Det hjælper med at bevare cachelagrede data, hvis en enkelt node mislykkes ved at vedligeholde flere kopier på andre noder.

Bemærk, at høj tilgængelighed reduceres overførselshastighed og øges ventetid på grund af opdateringer af den anden kopi på skriver. Det også fungerer mængden af hukommelse, der bruges til hvert element, så Planlæg for den pågældende. Dette bestemte eksempel vises, skal hver afhængige tjeneste muligvis funktioner, som kan forbedre din overordnede tilgængelighed og beskyttelse mod alvorlige fejl.

I hver afhængige tjeneste, skal du forstå konsekvenserne af en tjenesteforstyrrelse. I eksemplet med cachelagring, kan det være muligt at få adgang til dataene direkte fra en database, indtil du gendanner din cache. Dette ville være forringet tilstand i ydeevne, men kan give fuld funktionalitet med hensyn til data.

###<a name="region-wide-service-disruption"></a>Område hele tjenesteforstyrrelse

Forrige mislykkede forsøg er primært fejl, der kan administreres i det samme Azure område. Skal du forberede muligheden for, at der er en tjenesteforstyrrelse af det hele område. Hvis der opstår en område hele tjenesteforstyrrelse, er de lokalt overflødige kopier af dine data er ikke tilgængelige. Hvis du har aktiveret geografisk gentagelse, er der tre flere kopier af dine BLOB og tabeller i et andet område. Hvis Microsoft erklærer det område, der går tabt, knytter Azure alle DNS-poster til de replikerede geografisk område.

>[AZURE.NOTE] Vær opmærksom på, at du ikke har en hvilken som helst kontrol over denne proces, og det sker kun for region hele tjenesteforstyrrelse. På grund af dette, skal du stole på andre programmer-specifikke sikkerhedskopiering strategier til at opnå det højeste niveau af tilgængelighed. Yderligere oplysninger finder du i afsnittet på [data strategier for nedbrud](#data-strategies-for-disaster-recovery).

###<a name="azure-wide-service-disruption"></a>Azure hele tjenesteforstyrrelse

I planlægge efter nedbrud, skal du overveje hele området af mulige nedbrud. En af de mest alvorlige service afbrydelser medfører alle Azure områder samtidigt. Som med andre service forstyrrelser, kan du beslutter, du vil tage risikoen for midlertidige nedetid i så fald. Udbredte service afbrydelser, der spænder over områder skal være meget mere sjældent end isolerede service afbrydelser, der involverer afhængige tjenester eller enkelt områder.

For nogle vigtige programmer kan kan du dog beslutte, der skal være en plan for sikkerhedskopiering i dette scenarie samt. Planen for begivenheden, kan indeholde ned tjenester i en [alternativ skyen](#alternative-cloud) eller en [hybrid i det lokale miljø og skyen løsning](#hybrid-on-premises-and-cloud-solution).

###<a name="degraded-application-functionality"></a>Forringet programmets funktionalitet

Et flot program bruger typisk en samling af moduler, kommunikerer med hinanden via implementeringen af løst Koblede-udveksling mønstre. En DR-venlig programmet kræver adskillelse af opgaver på niveauet for modul. Dette er at forhindre, at afbrydelse af en afhængige tjeneste hvilket ned hele programmet. For eksempel overveje et webprogram commerce virksomhed y. Følgende moduler kan udgør programmet:

 * __Produktkatalog__ gør det muligt for brugerne at gennemse produkter.
 * Tilføj/fjern produkter i deres Indkøbsvogn brugerne __Indkøbsvogn__ .
 * __Rækkefølge Status__ viser leveringsstatus for bruger ordrer.
 * __Afsendelse af ordren__ Færdiggør i sessionen ved at sende rækkefølgen med betaling.
 * __Behandling af__ validerer rækkefølgen for dataintegritet og kontrollerer et antal tilgængelighed.

Hvis en afhængig af et modul i programmet går ned, hvordan modulet, der virker, indtil del genopretter? Et velkonstruerede system implementerer isolationsniveauet grænser til adskillelse af opgaver både i designfasen og på kørselstidspunktet. Du kan kategorisere alle fejl som genoprettelige og ikke kan gendannes. Uoprettelig fejl tager ned modulet, men du kan reducere en genoprettelig fejl gennem alternativer. Som beskrevet i afsnittet høj tilgængelighed, kan du skjule nogle af de problemer fra brugere ved håndtering af fejl, og det tager alternative handlinger. Under en mere alvorligt tjenesteforstyrrelse programmet muligvis ikke helt tilgængelig. En tredje mulighed er dog fortsat vedligeholdelse brugere i forringet tilstand.

Hvis databasen, vært ordrer går ned, mister modulet behandling eksempelvis muligheden for at behandle salg transaktioner. Afhængigt af arkitekturen, kan det være svært eller umuligt for afsendelse af ordren og behandling af dele af programmet for at fortsætte. Hvis programmet, ikke er beregnet til at håndtere dette scenario, kan hele programmet går offline.

Dog i dette samme scenario er det muligt, at produktdata er gemt i en anden placering. I så fald kan modulet produktkatalog stadig bruges til at få vist produkter. Programmet stadig være tilgængelige for brugere til tilgængelige funktioner som vist på produktkatalog i forringet tilstand. Andre dele af programmet, er dog ikke tilgængeligt, såsom rækkefølge eller lager forespørgsler.

En anden variation af forringet tilstand Centrerer på ydeevnen i stedet for funktioner. For eksempel bør du overveje et scenarie, hvor produktkataloget gemmes i cachen via Azure Redis Cache. Hvis cachelagring er tilgængelig, kan programmet gå direkte til server lagerplads til at hente katalog produktoplysninger. Men denne access muligvis langsommere end den cachelagrede version. På grund af dette, er programmet ydeevnen nedsat indtil cachelagring tjenesten er gendannet fuldstændigt.

Beslutte, hvor meget af et program fortsætter med at funktionen i forringet tilstand er både en forretningsbeslutninger og en tekniske beslutning. Programmet skal også beslutte, hvordan du kan give brugerne af de midlertidige problemer. I dette eksempel kan programmet Tillad visning af produkter og selv at føje dem til en indkøbskurv. Men, når brugeren forsøger at foretage et køb, programmet om brugeren, modulet Salg er midlertidigt ikke tilgængelig. Det er ikke velegnet til kunden, men den forhindrer en hele programmet tjenesteforstyrrelse.

##<a name="data-strategies-for-disaster-recovery"></a>Data strategier for nedbrud

Korrekt håndtering af data er området sværeste for at få højre i en hvilken som helst nedbrud gendannelse plan. Gendanne data er også en del af genoprettelsen, der tager typisk mest tid. Forskellige valgmuligheder i er forringet tilstande resulterer i svært udfordringer for genoprettelse af data fra fejl og konsistens efter fejl.

En af faktorerne, der er behov for at genoprette eller bevare en kopi af programmets data. Du kan bruge disse data til reference og transaktions formål på en sekundær websted. Et lokalt konfiguration kræver en dyr og længerevarende planlægningsprocessen at implementere en strategi for flere regioner nedbrud genoprettelse. De fleste udbydere i skyen, herunder Azure, Tillad nemt, let implementeringen af programmer til flere områder. Disse områder distribueres geografisk i en sådan måde, at flere regioner tjenesteforstyrrelse skal være meget sjælden. Strategi for håndtering af data på tværs af områder er en af de bidrager faktorer for en vellykket en nedbrud gendannelse plan.

I følgende afsnit beskrives nedbrud gendannelse teknikker relateret til data sikkerhedskopier, referencedata og transaktions data.

###<a name="backup-and-restore"></a>Sikkerhedskopiering og gendannelse

Regelmæssige sikkerhedskopier af application data kan understøtte nogle genoprettelse efter nedbrud scenarier. Forskellige lagerplads ressourcer kræver forskellige teknikker.

For lag Basic, som Standard og Premium SQL-Database, kan du drage fordel af punkt-in-time Gendan til at gendanne databasen. Du kan finde flere oplysninger, se [Oversigt: business løbende og database nedbrud med SQL-Database i skyen](../sql-database/sql-database-business-continuity.md). En anden mulighed er at bruge Active geografisk replikering for SQL-Database. Dette kopieres automatisk ændringer i databasen til sekundær databaser i det samme Azure område eller endda i et andet Azure område. Dette er en potentiel alternativ til nogle af de mere manuel data synkronisering teknikker præsenteres i denne artikel. Du kan finde flere oplysninger, se [Oversigt: SQL aktive geografisk-databasereplikering](../sql-database/sql-database-geo-replication-overview.md).

Du kan også bruge en mere manuel fremgangsmåde til at sikkerhedskopiere og gendanne. Bruge kommandoen DATABASEKOPI til at oprette en kopi af databasen. Du skal bruge denne kommando til at få en sikkerhedskopi med transaktions konsistens. Du kan også bruge tjenesten Importér/Eksportér til Azure SQL-Database. Dette understøtter eksport databaser til BACPAC filer, der er gemt i Azure Blob-lager.

Den indbyggede redundans Azure-lager opretter to kopier af den sikkerhedskopifil i det samme område. Dog bestemmer hyppigheden for kører sikkerhedskopieringen din frigivne Produktionsordre, som er mængden data, kan du miste i nedbrud scenarier. Forestil dig f.eks,, du opretter en sikkerhedskopi øverst i timen, og nedbrud opstår to minutter før øverst i timen. Du mister 58 minutter af data, der er foregået efter den sidste sikkerhedskopiering blev udført. For at beskytte mod en område hele tjenesteforstyrrelse, bør du også kopiere BACPAC filerne til et andet område. Du har derefter mulighed for at gendanne disse sikkerhedskopier i det andet område. Yderligere oplysninger finder du [Oversigt: business løbende og database nedbrud med SQL-Database i skyen](../sql-database/sql-database-business-continuity.md).

Azure-lager, kan du udvikle dine egne brugerdefinerede sikkerhedskopieringen eller bruge en af mange værktøjer til sikkerhedskopiering af tredjepart. Bemærk, at de fleste programmet design har flere kompleksiteterne ved hvor lagerplads ressourcer henviser til hinanden. For eksempel bør du overveje en SQL-database, der indeholder en kolonne, der linker til en blob i Azure-lager. Hvis sikkerhedskopierne ikke sker samtidigt muligvis databasen markøren til en blob, der ikke blev sikkerhedskopieret før fejlen. Programmet eller nedbrud gendannelse plan skal implementere processer for at håndtere denne uoverensstemmelse efter en genoprettelse.

###<a name="reference-data-pattern-for-disaster-recovery"></a>Reference datamønster for nedbrud

Referencedata er skrivebeskyttede data, der understøtter programmets funktionalitet. Typisk ændrer det ikke ofte. Selvom sikkerhedskopiering og gendannelse er en metode til at håndtere tjeneste i område hele afbrydelser, er RTO relativt lange. Når du installerer programmet til et sekundært område, kan nogle strategier forbedre RTO til referencedata.

Fordi henviser til dataændringer sjældent, du kan forbedre RTO ved at vedligeholde en permanent kopi af referencedataene i det sekundære område. Dette fjerner den tid, der kræves for at gendanne sikkerhedskopier i tilfælde af nedbrud. Hvis du vil opfylder flere regioner nedbrud gendannelse krav, skal du installere programmet og referencedataene sammen i flere områder. Som nævnt i [Reference datamønster for høj tilgængelighed](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), kan du installere referencedata til rollen sig selv eksterne lagerplads eller en kombination af begge.

Installation reference datamodellen i Beregn noder opfylder implicit nedbrud gendannelse kravene. Reference data installation til SQL-Database kræver, at du installerer en kopi af referencedata til hvert område. Den samme strategi gælder for Azure-lager. Du skal installere en kopi af en hvilken som helst referencedata, der er gemt i Azure-lager til de primære og sekundære områder.

![Reference data publikation til både primære og sekundære områder](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Du skal implementere din egen program-specifikke sikkerhedskopiering bortskaffelsesrutiner for alle data, herunder referencedata. Geografisk replikerede kopier på tværs af områder bruges kun i et område hele tjenesteforstyrrelse. For at undgå udvidet nedetid skal du installere de vigtige dele af programmets data i det sekundære område. Du kan finde et eksempel på denne topologi [aktiv / passiv model](#active-passive).

###<a name="transactional-data-pattern-for-disaster-recovery"></a>Transaktions datamønster for nedbrud

Implementering af et fuldt funktionelt nedbrud tilstand strategi kræver asynkron gentagelse transaktions dataene i det sekundære område. Vinduerne praktiske tid, inden for hvilken replikering kan opstå, bestemmer de frigivne Produktionsordre egenskaber af programmet. Du kan stadig gendanne de data, der er gået tabt fra det primære område under vinduet gentagelse. Du kan muligvis også flette med det sekundære område senere.

Eksemplerne nedenfor arkitektur indeholder nogle ideer på forskellige måder at håndtere transaktions data i et scenarie med failover. Det er vigtigt at Bemærk, at disse eksempler ikke omfattende. For eksempel blive mellemliggende lagerplads placeringer som køer erstattet med Azure SQL-Database. Køerne selv kan være enten Azure-lager eller Azure Service Bus køer (se [Azure køer og Service Bus køer – sammenlignet og i farvekategorier](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Server lagerplads destinationer kan også variere, såsom Azure tabeller i stedet for SQL-Database. Desuden kan indsættes arbejder roller som mellemled i forskellige trin. Det er vigtigt er ikke at emulere disse arkitekturer præcis, men du bør overveje forskellige alternativer i gendannelse af transaktions data og relaterede moduler.

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>Replikering af transaktions data med henblik på nedbrud

Overvej at et program, der bruger Azure-lager køer til at indeholde transaktions data. Dette giver mulighed for arbejder roller til at behandle transaktioner dataene til server-database i en Afkoblet arkitektur. Dette kræver transaktioner bruge nogle formular for midlertidige cachelager, hvis de front end roller kræver øjeblikkelig forespørgslen af de pågældende data. Afhængigt af niveauet af tolerance tab af data, kan du vælge at gentage køerne, databasen eller alle ressourcerne, der lagerplads. Med kun databasereplikering, hvis det primære område går ned, kan du stadig gendanne dataene i køerne når det primære område kommer tilbage.

I det følgende diagram viser en arkitektur, hvor server-databasen er synkroniseret på tværs af områder.

![Replikering af transaktions data med henblik på nedbrud](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

Den største udfordring at implementere denne arkitektur er gentagelse strategi mellem områder. Azure SQL Data Sync tjenesten giver denne type gentagelse. Men tjenesten stadig er i Vis udskrift og anbefales ikke til fremstilling miljøer. Du kan finde flere oplysninger, se [Oversigt: business løbende og database nedbrud med SQL-Database i skyen](../sql-database/sql-database-business-continuity.md). Du skal investere i en tredjepartsløsning fremstilling programmer eller Opret din egen gentagelse logik i kode. Afhængigt af arkitekturen være replikering tovejs, hvilket er også mere komplekse.

En potentiel implementering kan gøre brug af mellemliggende køen i det forrige eksempel. Rollen arbejder, der behandler data til den endelige lagerplads destination kan foretage ændringen i både det primære område og den sekundære område. De er ikke trivial opgaver, og komplet vejledning til gentagelse kode er ikke medtaget i denne artikel. Det vigtigste er det en masse tid og test skal fokusere på hvordan du gentage dine data til den sekundære område. Yderligere behandling og test af kan hjælpe med at sikre, at de failover og gendannelse processer korrekt håndtere eventuelle datauoverensstemmelser mulige eller dublerede transaktioner.

>[AZURE.NOTE] De fleste af dette dokument fokuserer på platform som en tjeneste (PaaS). Men bruge flere indstillinger for gentagelse og tilgængelighed til hybrid programmer virtuelle Azure-computere. Programmerne hybrid Brug infrastruktur som en tjeneste (IaaS) til at være vært for SQL Server på virtuelle maskiner i Azure. Det gør det traditionelle tilgængelighed fremgangsmåder i SQL Server, som AlwaysOn tilgængelighed grupper eller Log forsendelses. Nogle teknikker, som AlwaysOn, fungerer kun mellem forekomster af SQL Server i det lokale miljø og Azure virtuelle computere. Få mere at vide under [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####<a name="degraded-application-mode-for-transaction-capture"></a>Forringet tilstand for hentning af transaktion

Overvej at en anden arkitektur, der fungerer i forringet tilstand. Programmet på den sekundære område deaktiverer alle funktioner, som rapportering, business intelligence (BI), eller let køer. Den accepterer de vigtigste typer transaktions arbejdsprocesser, som defineret af virksomhedens behov. Systemet registrerer transaktionerne og skriver dem til køer. Systemet kan eventuelt udsætte databehandlingen under det første trin i tjenesteforstyrrelse. Hvis systemet på den primære område genaktiveres inden for det forventede tidsramme, kan rollerne, der arbejder i den primære region løbe køerne. Denne proces fjerner behovet for fletning af databasen. Hvis den primære område tjenesteforstyrrelse går ud over vinduet acceptable, kan programmet starte behandling køerne.

I dette scenarie skal indeholder databasen på sekundært trinvise transaktions data, som skal flettes, når primært genaktiveres. I følgende diagram vises denne strategi for gemmer transaktions data midlertidigt, indtil det primære område genoprettes.

![Forringet tilstand for hentning af transaktion](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Finde flere beskrivelse af teknikker til administration af data for tolerant Azure-programmer, [fejlsikret: vejledning til tolerant skyen arkitekturer](https://channel9.msdn.com/Series/FailSafe).

##<a name="deployment-topologies-for-disaster-recovery"></a>Installation topologier for nedbrud

Du skal forberede kritiske programmer mulighed for et område hele tjenesteforstyrrelse. Du kan gøre dette ved at inkorporere en strategi for installation af flere regioner i funktionsdygtige planlægning.

Flere regioner installationer kan omfatte IT pro processer for at udgive programmet og reference dataene på den sekundære område efter nedbrud. Hvis programmet kræver Chat failover, kan installationsprocessen omfatte en aktiv/passiv konfiguration eller en aktiv/aktiv konfiguration. Denne type installation har eksisterende forekomster af det program, der kører i et andet område. Et routing værktøj som Azure trafik Manager indeholder belastning tjenester på niveauet for DNS. Det kan registrere tjenesten afbrydelser og omdirigere brugerne til forskellige områder, når det er nødvendigt.

En del af en vellykket Azure nedbrud planlagde arkitektur pågældende gendannelse til løsning fra start. Skyen giver yderligere indstillinger for at gendanne fra fejl under nedbrud, der ikke er tilgængelige i en traditionel vært. Nærmere betegnet kan kan du hurtigt og dynamisk tildele ressourcer til et andet område. Derfor betaler ikke du meget for inaktiv ressourcer, mens du venter til en fejl, der skal udføres.

De følgende afsnit dækker forskellige installation topologier for nedbrud. Typisk, der findes en fordeling i øgede omkostninger eller kompleksitet for yderligere tilgængelighed.

###<a name="single-region-deployment"></a>Ét område installation

En enkelt område installation er ikke virkelig en nedbrud gendannelse topologi, men er meningen, at kontrast med de andre arkitekturer. Ét område installationer er fælles for programmer i Azure. Denne type installation er dog ikke en alvorlige bud på en nedbrud gendannelse plan.

Følgende diagram viser et program, der kører i et enkelt Azure område. Øge tilgængeligheden af programmet i området, Azure trafik overordnet og brug af fejl og opgradering af domæner.

![Ét område installation](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Her, er det synlige der databasen for fejl fra ét sted. Selvom Azure kopieres data på tværs af forskellige fejl domæner til interne replikaer, opstår hele i det samme område. Programmet kan klare opstår nedbrud. Hvis området, der går ned, alle fejl domænerne gå ned – herunder alle forekomster af tjenesten og lagerplads ressourcer.

For alle men mindst vigtige programmer, skal du udarbejde en plan til at udrulle dine programmer på tværs af flere områder. Du skal også overveje RTO og omkostninger begrænsninger overvejer, hvilket installation topologi for at bruge.

Lad os se nærmere nu på bestemte mønstre til at understøtte failover på tværs af forskellige områder. Disse eksempler alle bruger to områder til at beskrive processen.

###<a name="redeployment-to-a-secondary-azure-region"></a>Geninstallation til et sekundært Azure område

I anvendelse af geninstallation til et sekundært område har det primære område programmer og databaser, der kører. Sekundær område er ikke konfigureret for en automatisk failover. Når nedbrud opstår, skal du så rotation af alle delene af tjenesten i det nye område. Dette omfatter overførsel af en skybaseret tjeneste til Azure, implementering skytjenesten, gendannelse af data og ændrer DNS for at omdirigere trafikken.

Selvom det er den lavest mulige af indstillingerne for flere regioner, har den værste RTO karakteristika. I denne model, pakke og database sikkerhedskopier af tjenesten gemmes enten lokalt eller i Azure Blob storage forekomsten af den sekundære område. Du skal installere en ny tjeneste og gendanne dataene, før den genoptages handling. Selvom du fuldautomatisk dataoverførsel fra ekstra lagerplads, forbruger ideer op det nye miljø database en masse tid. Flytte data fra ekstra plads til den tomme database på den sekundære område er den mest dyr del af gendannelsen. Du skal gøre dette, men for at få den nye database til en funktionel tilstand, fordi den ikke replikeres.

Den bedste metode er at gemme servicepakker i Blob-lager i det sekundære område. Dette behovet for at overføre pakken til Azure, som er, hvad der sker, når du installerer fra en lokal udvikling maskine. Du kan hurtigt installere servicepakker til en ny skybaseret tjeneste fra Blob-lager ved hjælp af PowerShell-scripts.

Denne indstilling er praktisk kun for ikke-kritisk programmer, der kan acceptere en høj RTO. Det kan for eksempel virker for et program, der kan være nede i flere timer, men skal køres igen inden for 24 timer.

![Geninstallation til et sekundært Azure område](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>Aktiv / passiv

Aktiv / passiv mønster er den valgmulighed, der mange virksomheder prioriteret først. Dette mønster leverer forbedringer til RTO med en lille forøgelse omkostninger over geninstallation mønster.
I dette scenarie er der igen en primær og en sekundær Azure område. Alle trafikken går til den aktive installation på den primære område. Sekundær område er bedre forberedt på nedbrud, fordi databasen, der kører på begge områder. Desuden er en synkroniseringsfunktion på plads mellem dem. Denne standby metode, kan det omfatte to variationer: en database kun tilgang eller en komplet installation i det sekundære område.

####<a name="database-only"></a>Kun-database

I den første variation aktiv / passiv mønsterets har det primære område et udløst skyen tjenesteprogram. I modsætning til mønster geninstallation synkroniseres begge områder dog med indholdet af databasen. (Du kan finde flere oplysninger, du se afsnittet på [vist rapportdata mønster for nedbrud](#transactional-data-pattern-for-disaster-recovery).) Når der opstår nedbrud, er der færre aktivering krav. Du starter programmet i det sekundære område, ændre strenge til den nye database og ændre DNS-poster for at omdirigere trafik.

Som geninstallation mønsteret, bør du allerede har gemt servicepakker på Azure Blob-lager i det sekundære område til hurtigere installation. I modsætning til mønster geninstallation betale ikke du for størstedelen af de omkostninger, der kræver gendannelse af databasen. Databasen er klar og kører. Dette gemmer en lang tid, og det gør en økonomisk DR mønster. Det er også de mest populære DR mønster.

![Aktiv / passiv-database](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>Fuld replika

I den anden variation aktiv / passiv mønsterets har både det primære område og den sekundære område en komplet installation. Denne installation omfatter skytjenesterne og en synkroniseret database. Det primære område er aktivt håndtering netværksanmodninger fra brugere. Sekundær område aktiveres, kun, når det primære område har en tjenesteforstyrrelse. I så fald omdirigere alle nye netværksanmodninger til den sekundære område. Azure trafik Manager kan administrere denne failover automatisk.

Failover opstår hurtigere end-database kun variation, fordi tjenesterne, har du allerede er installeret. Dette mønster giver en meget lav RTO. Sekundær failover område skal være klar til at gå umiddelbart efter fejl i forbindelse med det primære område.

Sammen med en hurtigere svartid har dette mønster fordelen ved udfyldt fordeling og implementere ekstra tjenester. Du behøver at bekymre dig om et område, der ikke har plads til at tildele nye forekomster i nedbrud. Dette er vigtigt, hvis dit sekundær Azure område er ved at nå kapacitet. Der er ingen garanti (serviceniveauaftale), du øjeblikkeligt kan installere et antal nye skytjenester på en hvilken som helst område.

Den hurtigste svartid med denne model, skal du har lignende skala (antallet af forekomster af rolle) i de primære og sekundære områder. Betaler for ubrugte Beregn forekomster er dyrt på trods af fordelene, og det er ikke nødvendigvis det mest forsigtig finansielle valg. På grund af dette er det mere almindeligt at bruge en lidt skaleret ned version af skytjenester på den sekundære område. Du kan hurtigt skifte over og skalere ud af den sekundære installation, hvis det er nødvendigt. Du bør automatisere failover-processen, så når det primære område er ikke tilgængelig, skal du aktivere flere forekomster, afhængigt af belastningen. Dette kan omfatte brug af en Autoskalering ordning som [virtuelt skala angiver](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

I det følgende diagram viser den model, hvor de primære og sekundære områder indeholder en fuldt udløst skybaseret tjeneste i en aktiv / passiv mønster.

![Aktiv / passiv, fuld replika](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>Aktiv-aktiv

På nuværende tidspunkt, du sandsynligvis forståelse af udviklingen af mønstrene: reducere RTO øges omkostninger og kompleksitet. Aktiv-aktiv løsningen sideskift faktisk denne tendens med hensyn til omkostninger.

I en aktiv-aktiv mønster installeret skytjenester og database fuldt ud i begge områder. I modsætning til aktiv / passiv modellen modtager begge områder bruger trafik. Denne indstilling giver den hurtigste gendannelse tid. Tjenesterne, der skaleres allerede til at håndtere en del af belastningen på hvert område. DNS er allerede aktiveret til at bruge det sekundære område. Der findes flere kompleksitet i bestemme, hvordan man omdirigere brugere til det pågældende land. Round robin-planlægning kan være muligt. Det er større sandsynlighed for, at bestemte brugere skal bruge et bestemt område, hvor den primære kopi af deres data er placeret.

I tilfælde af failover, skal du blot Deaktiver DNS til det primære område. Dette dirigerer al trafik til den sekundære område.

Selv i denne model er der nogle variationer. For eksempel viser i det følgende diagram en model hvor det primære område ejer master kopien af databasen. Skytjenester i begge områder skrive til den primære database. Den sekundære installation kan læse fra den primære eller replikerede database. Gentagelse i dette eksempel sker én måde.

![Aktiv-aktiv](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Der findes en Ulempen til den aktive aktive arkitektur i det foregående diagram. Det andet område skal åbne databasen i det første område, fordi den overordnede kopi er placeret der. Ydeevnen falder betydeligt fra når du opretter adgang til data fra uden for et område. I tværs område database opkald, skal du overveje nogle typer samling strategi for at forbedre ydeevnen for disse opkald. Se, [hvordan du bruger "samling" for at forbedre ydeevnen i SQL-Database programmet](../sql-database/sql-database-use-batching-to-improve-performance.md)kan finde flere oplysninger.

En alternativ arkitektur kan omfatte hvert område, får direkte adgang til sin egen database. I modellen, er en type af tovejs gentagelse kræves for at synkronisere databaser i hvert område.

I det aktive aktive mønster nødvendigvis du ikke så mange forekomster på den primære område, som du ville gøre i det aktive passive mønster. Hvis du har 10 forekomster på den primære område i en aktiv / passiv arkitektur, skal du muligvis kun 5 i hvert område i en aktiv-aktiv arkitektur. Begge områder nu dele afkrydsningsfeltet Indlæs. Det kan være en penge over aktiv / passiv mønster, hvis du har gemt en varm standby på passive område med 10 forekomster, der venter på failover.

Opdager, indtil du gendanner det primære område, kan det sekundære område modtage en pludselig forekomst af nye brugere. Hvis der er 10.000 brugere på hver server, når det primære område oplever et tjenesteforstyrrelse, har det sekundære område pludselig til at håndtere 20.000 brugere. Overvågning regler på den sekundære område skal registrere denne forøgelse og dobbelt forekomster i det sekundære område. Du kan finde flere oplysninger om dette i afsnittet på [registrering af fejl](#failure-detection).

##<a name="hybrid-on-premises-and-cloud-solution"></a>Hybrid i det lokale miljø og skyen løsning

Er en ekstra strategi for nedbrud at udvikle en hybrid-program, der kører lokalt og i skyen. Afhængigt af programmet, kan det primære område være enten placering. Kan du overveje de forrige arkitekturer og Forestil dig det primære eller sekundære område som en lokal placering.

Der er nogle af de udfordringer i disse hybrid arkitekturer. Først skal de fleste af denne artikel er adresseret PaaS arkitektur mønstre. Typisk PaaS programmer i Azure bruger Azure-specifikke konstruktioner som roller, skytjenester og trafik Manager. Hvis du vil oprette en lokal løsning til denne type PaaS programmet kræver en signifikant forskellige arkitektur. Dette kan muligvis ikke muligt fra en management eller omkostninger perspektiv.

En hybridløsning til nedbrud har dog færre udfordringer for traditionelle arkitekturer, der er blot flyttet til skyen. Dette er sandt om arkitekturer, der bruger IaaS. IaaS programmer bruger virtuelle maskiner i skyen, kan have direkte lokalt tilsvarende. Du kan også bruge virtuelle netværk til at oprette forbindelse computere i skyen med lokale netværksressourcer. Dette åbner flere muligheder, der ikke er muligt med programmer, der kun PaaS. SQL Server kan for eksempel drage fordel af løsninger til genoprettelse efter genoprettelse som AlwaysOn tilgængelighed grupper og databasespejling. Yderligere oplysninger finder du [høj tilgængelighed og nedbrud for SQL Server på Azure virtuelle computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

IaaS løsninger giver også en nemmere sti til lokale programmer til at bruge Azure som indstillingen failover. Du kan have et fuldt funktionelt program på et eksisterende lokalt område. Men hvad nu, hvis du har ikke finde vejledningen til at bevare en geografisk separat område til failover? Du kan vælge at bruge virtuelle maskiner og virtuelle netværk til at få dit program, der kører i Azure. Det er tilfældet, definere processer, der skal synkroniseres data til skyen. Azure installationen derefter bliver den sekundære område, der skal bruge til failover. Det primære område forbliver programmet i det lokale miljø. Du kan finde flere oplysninger om IaaS arkitekturer og egenskaber, [virtuelle maskiner dokumentation](https://azure.microsoft.com/documentation/services/virtual-machines/).

##<a name="alternative-cloud"></a>Alternativ skyen

Der er visse situationer, hvor selv stabilitet Microsoft cloud ikke opfylder muligvis interne overholdelse af regler eller politikker, der kræver, at din organisation. Selv den bedste forberedelse og design til at implementere sikkerhedskopiering systemer under nedbrud falde kort, hvis der er en global tjenesteforstyrrelse af en udbyder af skyen.

Du vil sammenligne af tilgængelighedskrav med omkostningerne og kompleksiteten af Øget tilgængelighed. Analyse af risiko, og Definer RTO og frigivne Produktionsordre til din løsning. Hvis dit program ikke kan acceptere en hvilken som helst nedetid, kan det giver mening for dig at overveje at bruge en anden skybaseret løsning. Medmindre på hele internettet går ned, kan en anden skybaseret løsning stadig være tilgængelige, hvis Azure utilgængeligt globalt.

Som i scenarie hybrid findes failover installationer i forrige nedbrud gendannelse arkitekturer også i en anden skybaseret løsning. Alternativ skyen DR websteder skal bruges kun til løsninger, hvis RTO gør det meget lille, hvis der overhovedet skal nedetid. Bemærk, at en løsning, der bruger et DR websted uden for Azure kræver mere arbejde at konfigurere, udvikle, implementere og vedligeholde. Det er også sværere at implementere bedste fremgangsmåder i en arkitektur i tværs skyen. Selvom skyen platforme har lignende overordnet begreber, er API'er og arkitekturer forskellige.

Hvis du beslutter at opdele din DR mellem forskellige platforme, vil det giver mening at udvikle fremstilling lag i designet af løsningen. Hvis du gør dette, skal du ikke at udvikle og vedligeholde to forskellige versioner af det samme program til forskellige skyen platforme i tilfælde af nedbrud. Som med scenarie hybrid kan brugen af virtuelle Azure-computere eller Azure objektbeholder tjenesten være nemmere i disse tilfælde end ved brug af cloud-specifikke PaaS design.

##<a name="automation"></a>Automatisering

Nogle af de mønstre, som kun omtalt kræver hurtig aktivering af offline installationer og gendannelse af bestemte dele af et system. Automatisering eller scripting, understøtter muligheden for at aktivere ressourcer efter behov og implementere løsninger hurtigt. I denne artikel, DR-relaterede automatisering svarede med [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx), men [Service Management REST-API](https://msdn.microsoft.com/library/azure/ee460799.aspx) er også en indstilling.

Udvikling af scripts, som hjælper med at administrere delene af DR, Azure ikke håndterer transparent. Dette er fordelene ved at producere ensartede resultater, hver gang, hvilket minimerer risikoen for mennesker fejl. Har du foruddefinerede DR scripts reducerer også tid at genopbygge et system og de enkelte bestanddele midt på nedbrud. Du vil ikke forsøge at manuelt finde ud af, hvordan du kan gendanne dit websted, mens det er ned og tabende penge hvert minut.

Når du opretter scriptene, test af dem flere gange fra start til slut. Når du har kontrolleret deres grundlæggende funktionalitet, Sørg for, at du tester dem i [nedbrud simulering](#disaster-simulation). Dette hjælper med at afdække fejl i scripts eller processer.

Den bedste fremgangsmåde med automation er at oprette et lager af PowerShell-scripts eller kommandolinjen (CLI) scripts til Azure nedbrud. Tydeligt markere og kategorisere dem for nem opslag. Angive en person til at administrere lager og versionsstyring af scriptene. Dokument dem godt med beskrivelser af parametre og eksempler på brug af script. Også sørge for, at du beholder denne dokumentation synkroniseret med din Azure installationer. Dette understregningstegn formålet med har én person ansvarlig for alle dele af lageret.

##<a name="failure-detection"></a>Registrering af fejl

Hvis du vil håndtere problemer med tilgængeligheden og nedbrud korrekt, skal du kunne registrere og diagnosticere mislykkede forsøg. Du skal gøre avancerede server- og installation overvågning, så du hurtigt kan se, når et system eller delene er pludselig ned. Værktøjer, som kigger på den overordnede sundhed for skytjenesten og dens afhængigheder kan udføre en del af dette arbejde. En Microsoft-værktøjet er [System Center 2016](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/). Tredjepartsværktøjer kan også angive overvågning funktioner. De fleste overvågning løsninger spore KPI'er tællere og tjenesten kører.

Selvom disse værktøjer er vigtig, erstatter de ikke at skulle planlægge til registrering af fejl og rapportering i en skybaseret tjeneste. Du skal planlægge at bruge korrekt Azure diagnosticering. Brugerdefineret ydeevne tællere eller -hændelseslog poster kan du også være en del af den overordnede strategi. Dette giver flere data under mislykkede forsøg til hurtigt at undersøge problemet og gendanne fuld funktioner. Den indeholder også flere statistikker, overvågning værktøjerne kan bruge til at bestemme programmet tilstand. Få mere at vide under [Aktivere Azure Diagnostics i Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Finde en beskrivelse af, hvordan du planlægge en samlet "sundhedstilstandsmodel" [fejlsikret: vejledning til tolerant skyen arkitekturer](https://channel9.msdn.com/Series/FailSafe).

##<a name="disaster-simulation"></a>Nedbrud simulering

Test af simulering omfatter oprettelse af små virkelige liv situationer på arbejde floor til at se, hvordan gruppemedlemmerne reagerer. Simulering også vise, hvor effektive løsningerne, der er i den gendannelse plan. Udføre simulering på en sådan måde, de oprettede scenarier ikke forstyrre faktiske business mens du stadig følelse som reelle situationer.

Overvej at planlagde arkitektur en slags "Oversigt" i programmet på computeren til at simulere manuelt problemer med tilgængeligheden. Udløse eksempelvis database access undtagelser for et bestilling modul via en blød switch ved, at den ikke fungerer korrekt. Du kan bruge lignende lette hjælpemidler til andre moduler på niveauet for interface netværk.

Simuleringen fremhæver de problemer, der blev inadequately omtalt. Simuleret scenarier være helt kan kontrolleres. Det betyder, selvom den gendannelse plan ser ud til at fejl, du kan gendanne situationen tilbage til normal uden at forårsage betydeligt skader. Det er også vigtigt, at du informerer et højere ledelsen om hvornår og hvordan simulering øvelserne vil blive udført. Denne plan skal indeholde oplysninger om tid eller ressourcer, der kan blive passiv, mens der kører simulering testen. Det er også vigtigt at definere, hvordan bliver er muligt at måle succes, når du en din nedbrud gendannelse plan til en test.

Der findes flere andre teknikker, du kan bruge til at teste nedbrud gendannelse planer. De fleste af dem er dog blot ændrede versioner af disse grundlæggende teknikker. Den primære interesse bag denne test er at evaluere hvordan muligt, og hvordan realistisk gendannelse plan er. Nedbrud test fokuserer på detaljer for at finde huller i den grundlæggende gendannelse plan.

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en række artikler fokuseret på [nedbrud og høj tilgængelighed for bygget på Microsoft Azure-programmer](./resiliency-disaster-recovery-high-availability-azure-applications.md). Den forrige artikel i denne serie er [høj tilgængelighed for bygget på Microsoft Azure-programmer](./resiliency-high-availability-azure-applications.md).
