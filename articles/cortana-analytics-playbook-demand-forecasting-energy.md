<properties
    pageTitle="Cortana Intelligence løsning skabelon Playbook til demand prognose energi | Microsoft Azure"
    description="En løsning skabelon med Microsoft Cortana Intelligence, der hjælper forecast behov for en energi utility virksomhed."
    services="cortana-analytics"
    documentationCenter=""
    authors="ilanr9"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/24/2016"
    ms.author="ilanr9;yijichen;garye"/>

# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence løsning skabelon Playbook til Demand prognose energi  

## <a name="executive-summary"></a>Overordnet konklusion  

I de seneste år, har flettet Internet af ting (IoT), alternativ energi og stor data for at oprette store salgsmuligheder i utility og energi domænet. Værktøjet og energisektoren hele har set forbrug udjævne med forbrugere, der kræver bedre måder til at styre deres brug af energi på samme tid. Det vil sige, at utility og smart gitter firmaer er i god behov for at forny og forny sig selv. Desuden bliver mange power og utility gitre forældede og meget store til at vedligeholde og administrere. I det sidste år, har teamet arbejdet på en række aftaler i energi domæne. Vi er stødt på mange situationer, hvor har leder du værktøjer eller softwareudviklere (uafhængige softwareleverandører) til prognoser til behovet for fremtidige energi under disse aftaler. Disse prognoser afspille en vigtig rolle i deres nuværende og fremtidige virksomhed og er blevet grundlaget for forskellige Brug sager. Dette omfatter kort- og langsigtede power Indlæs prognose, handel, belastningsjustering, gitter optimering osv. Big data og avancerede Analytics (å) metoder som Machine Learning (ML) er de vigtigste enablers til at lave præcise og pålidelig prognoser.  

I denne playbook vi sammensat business og analytical retningslinjer, der kræves for en vellykket udvikling og installation af behovet for energi forecast-løsning. Disse foreslåede retningslinjer kan hjælpe med værktøjer, data forskere og data teknikere til at oprette løsninger, der er fuldt opgivet, skybaseret, demand prognoser. Sådan en løsning kan repræsentere startbasis i deres langsigtede smart gitter strategi for virksomheder, der lige er begyndt deres big data og avancerede analytics rejse.

>[AZURE.TIP] For at hente et diagram, der indeholder en arkitektonisk oversigt over denne skabelon, skal du se [Cortana Intelligence løsning skabelon arkitektur til demand prognose energi](cortana-analytics-architecture-demand-forecasting-energy.md).  

## <a name="overview"></a>Oversigt  

Dette dokument omhandler business, data og tekniske aspekter ved at bruge Cortana Intelligence og i bestemt Azure Machine Learning (AML) til implementering og installation af energi prognoser løsninger. Dokumentet består af tre hoveddele:  

1. Forstå Business  
2. Forstå data  
3. Tekniske gennemførelse

Webdelen **Business om** skitseres det business aspekt et skal forstå og overveje, før du foretager en investering beslutning. Det beskriver, hvordan du kvalificerer business problemet ved hånden for at sikre, at skønnet analyser og machine learning er faktisk effektive og det er relevant. Dokument yderligere forklares de grundlæggende regler for maskine læring og hvordan de bruges til at løse problemer med at energi prognoser. Beskriver forudsætningerne og kvalifikation kriterierne i en use case. Nogle eksempel bruge tilfælde og forretningsmulighed scenarier findes også.

Data er den primære datablad for en hvilken som helst computer læ løsning. **Data om** en del af dette dokument omhandler nogle vigtige aspekter af dataene. Det beskriver type data, der skal bruges til energi prognoser, data kvalitetskrav og hvilke datakilder der typisk findes. Vi kan også forklarer, hvordan den rækkedata bruges til at forberede datafunktioner, der faktisk drive delen modellering.

Den tredje del af dokumentet dækker **Tekniske gennemførelse** aspekter af en løsning. Funktionen teknisk og modellering er kernen i processen data videnskab og derfor der beskrives i nogle detaljer. Det dækker begrebet webtjenester, som er et vigtigt biltype til skyen implementering af skønnet analytics-løsninger. Vi disposition også en typisk arkitektur af en operationalized til en komplet løsning.

Dokumentet indeholder desuden referencemateriale, som du kan bruge til at få yderligere forståelse af domæne og teknologi.

Det er vigtigt at være opmærksom på, at vi ikke vil dække i dette dokument dybere data videnskabelige processen, dens matematiske og tekniske aspekter. Disse oplysninger kan findes i [Azure ML dokumentation](http://azure.microsoft.com/services/machine-learning/) og [blogge](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Målgruppe   
Målgruppen for dette dokument er både business og tekniske personale, som vil få viden og forståelse af Machine Learning baseret løsninger, og hvordan disse bliver brugt specifikt i domænet energi prognoser.

Data forskere kan også drage fordel af læse dette dokument for at få en bedre forståelse af processen højt niveau, der styrer installation af en prognose løsning energi. I denne kontekst det kan også bruges til at oprette en god oprindelig plan og udgangspunktet for mere detaljeret og avancerede materiale.

### <a name="industry-trends"></a>Branchetendenser  
I de seneste år, har flettet IoT, alternative energi og stor data for at oprette store salgsmuligheder i pladsen til utility og energi. Værktøjet og hele energi sektorer har set forbrug udjævne med forbrugere, der kræver bedre måder til at styre deres brug af energi på samme tid.

Mange utility og smart energi firmaer har pionerer inden for [smart gitter](https://en.wikipedia.org/wiki/Smart_grid) ved at installere et antal Brug tilfælde, som gør brug af de data, der genereres af gitteret. Mange Brug tilfælde drejer sig om el naturlige karakteristika: kan ikke akkumuleret eller gemt Afsæt som lager. Men hvad er produceret skal bruges. Funktioner, der skal være mere effektiv skal forecast strømforbruget bare fordi, der kan give dem større mulighed for at **balance levering og behov**, hvilket forhindrer energi årsager, **reducere drivhusgasser gas emission**og kontrolelementet omkostninger.

Når du taler af omkostninger, er der et andet vigtigt aspekt, som er pris. Nye muligheder for at handel power mellem funktioner har hentet ind i et godt formål at **forudsige fremtidige efterspørgsel og fremtidige pris el**. Dette kan hjælpe firmaer bestemme deres fremstilling enheder.

Når vi bruger ordet 'smart', henviser vi faktisk til et gitter, der kan få mere at vide og derefter foretage prognoser. Det kan forventer at skulle sæsonbestemte ændringer i forbrug samt **forudse midlertidige overbelastning situationer og automatisk at justere for den**. Lokaliseret overbelastning situationer kan løses ved fra en fjernplacering vedrørende forbrug (ved hjælp af disse smart målere). **Ved først at forudsige og derefter fungerer**, gitteret gør selve smartere over tid.

For resten af dette dokument, som vi fokuserer på en bestemt familie use cases, der dækker forudsigelse i fremtiden, kort sigt og langsigtede behovet for energi. Vi har arbejdet på disse områder i et par måneder og har fået nogle viden og kompetence, der giver os mulighed for medføre branche grade resultater. Andre Brug tilfælde behandles samt i dokumentet inden for kort tid.

## <a name="business-understanding"></a>Forstå Business

### <a name="business-goals"></a>Virksomhedens mål
**Energi Demo** målet er at vise en typisk skønnet analyser og machine learning-løsning, der kan anvendes i en meget kort tidsramme. Nærmere betegnet kan handler vores aktuelle fokus om aktivering energi demand prognose løsninger, så dens forretningsværdi kan hurtigt realiseres og opgraderede på. Oplysningerne i dette playbook kan hjælpe dig med at udføre følgende mål kunden:
-   Kort klokkeslætsformat med værdien af machine learning baseret løsning
-   Muligheden for at udvide et pilotprojekt brugseksempel til andre Brug tilfælde eller til et bredere omfang, der er baseret på deres forretningsmæssige behov
-   Få hurtig Cortana Intelligence pakke viden om produktet

Med disse mål, denne playbook har til formål at levere business og tekniske viden, der hjælper med at nå disse mål.

### <a name="power-load-and-demand-forecasting"></a>Power-indlæses og Demand prognoser
I energisektoren, kan der være mange måder efterspørgsel prognoser kan hjælpe med at løse problemer med at virksomhedens vigtige. Faktisk kan demand prognoser betragtes grundlaget for mange core Brug sager i branchen. Generelt, vi overveje to typer energi demand prognoser: kort sigt og langtidsaftale. Hver enkelt kan fungere noget andet og bruge en anden metode. Primære forskellen mellem to er laver prognose forude, hvilket vil sige tidsrummet i fremtiden, vil vi forecast.

#### <a name="short-term-load-forecasting"></a>Kort ord Indlæs prognoser
I forbindelse med behovet for energi, er korte ord indlæse prognoser (STLF) defineret som den samlede belastning, som er forecast i nær fremtid på forskellige dele af gitteret (eller gitteret som helhed). I denne sammenhæng defineres korte ord for at være tid forude inden for området til 1 time til 24 timer. I nogle tilfælde er en horisont for 48 timer også muligt. Derfor er STLF meget almindeligt i en funktionsdygtige use case i gitteret. Her er nogle eksempler på STLF indsatsbaserede Brug tilfælde:
-   Levering og behov justering af
-   Power handel support
-   Marked foretage (indstilling power pris)
-   Gitter funktionsdygtige optimering
-   [Demand svar](https://en.wikipedia.org/wiki/Demand_response)
-   Hastigheder under spidsbelastning demand prognoser
-   Administration af efterspørgsel side
-   Justering af belastning og overbelastning forebyggelse
-   Lang sigt Indlæs prognoser
-   Fejl og anomali registrering
-   Spidsbelastning reparationsværft/udjævning 

STLF model er hovedsageligt baseret på nær tidligere (sidste dag eller uge) forbrugsdata og brug forecast temperaturen som en vigtig indikator for stigning. Bestil nøjagtige temperaturen budget for næste time og op til 24 timer er ved at blive mindre af en udfordring nu dage. Disse modeller er mindre følsomme til sæsonbestemte mønstre eller langsigtede forbrug tendenser.

SLTF-løsninger, der er også sandsynlighed for at generere stor mængde forudsigelse opkald (serviceanmodninger) da de der aktiveres på grundlag af hver time og i nogle tilfælde selv med højere frekvens. Det er også meget almindeligt at se implantation, hvor hver enkelt understation eller transformer repræsenteres som en enkeltstående model, og derfor lydstyrken for anmodninger om Tekstfuldførelse er endnu mere.

#### <a name="long-term-load-forecasting"></a>Lang sigt Indlæs prognoser
Målet af lang sigt Indlæs prognoser (LTLF) er at forudsige power demand med en tid forude, lige fra 1 uge, flere måneder (og i nogle tilfælde for et antal år). Denne række forude bruges hovedsageligt til planlægning og investering use cases.

Det er vigtigt at have høj kvalitetsdata, der dækker et tidsinterval, flere år (mindst 3 år) for langsigtede scenarier. Disse modeller typisk udtrækker sæsonudsving mønstre fra de historiske data og gøre brug af eksterne predicators, sådan som vejr og såfremt mønstre.

Det er vigtigt at gøre det klart, at jo længere laver prognose forude er, jo mindre præcis muligvis prognosen. Det er derfor vigtigt at producere nogle sikkerhedsgrænser sammen med den faktiske prognose, der tillader mennesker med forhold mulige variation i deres planlægningsprocessen.

Da scenariet forbrug for LTLF hovedsageligt planlægning, kan vi forvente meget lavere forudsigelse enheder (i forhold til STLF). Vi vil typisk se disse prognoser, der er integreret i Visualiseringen funktioner som Excel eller PowerBI og skal startes manuelt af brugeren.

### <a name="short-term-vs-long-term-prediction"></a>Korte ord kontra lang sigt forudsigelse
Følgende tabel sammenligner STLF og LTLF i forhold til de vigtigste attributter:

|Attribut|Forecast-kort sigt indlæsning|Lang sigt Indlæs prognose|
|---|---|---|
|Forecast horisont|Fra 1 time til 48 timer|Fra 1 til 6 måneder eller derover|
|Granulariteten for data|Hver time|Hver time eller dagligt|
|Typisk anvendelse sager|<ul><li>Demand/levering justering af</li><li>Vælg time prognoser</li><li>Demand svar</li></ul>|<ul><li>Lang sigt planlægning</li><li>Gitter Aktiver planlægning</li><li>Ressourceplanlægning</li></ul>|
|Typisk predictors|<ul><li>Dag eller uge</li><li>Time i dag</li><li>Hver time temperaturen</li></ul>|<ul><li>Måned i året</li><li>Dag i måneden</li><li>Langtidsaftale temperatur og såfremt</li></ul>|
|Historiske dataområde|To eller tre års værd af data|5-10 år værd af data|
|Typisk nøjagtighed|MAPE * på 5% eller derunder|MAPE * på 25% eller derunder|
|Prognose frekvens|Produceret hver time eller døgnet|Produceret når månedlig, kvartalsvis eller årlig|
\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – middelværdien gennemsnitlige procent fejl

Det er meget vigtigt at skelne mellem kort og lange ordet prognoser scenarier, som disse repræsenterer forskellige forretningsmæssige behov og kan have forskellige installation og forbrugsmønstre, som kan ses fra denne tabel.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Eksempel Use Case 1: eSmart systemer – overbelastning optimering
En vigtig regel i et [smart gitter](https://en.wikipedia.org/wiki/Smart_grid) er at dynamisk og konstant optimere og tilpasse for de ændrede forbrugsmønstre. Strømforbruget kan blive påvirket af kortvarig ændringer, som hovedsageligt skyldes temperaturudsving (*f.eks.*, større styrke bruges til air betingelse eller varme). På samme tid påvirkes strømforbruget også langsigtede tendenser. Disse kan omfatte sæsonudsving effekter, Helligdage, langsigtede forbrug vækst og endda økonomiske faktorer som forbrugere indeks, oil pris og BNP.

I denne use case ønskede [eSmart](http://www.esmartsystems.com/) at installere en skybaseret løsning, der gør det muligt at forudsige tilbøjeligheden af en overbelastningssituation på en given understation i gitteret. Især ville eSmart til at identificere fordelingsstationer, der sandsynligvis overbelastning inden for den næste time, så en øjeblikkelig handling kan blive ført til at undgå eller løse denne situation.

En nøjagtig og hurtigt udføre forudsigelse kræver implementering af tre skønnet modeller:
-   Lange ord-model, der gør det muligt for prognoser af strømforbruget på hver understation i løbet af næste par uger eller måneder
-   Kort sigt model, der gør det muligt for forudsigelse af overbelastningssituation på en given understation under næste time
-   Temperaturen model, der giver prognoser af fremtidige temperaturen over flere scenarier

Formålet med langsigtede modellen er ved at placere fordelingsstationerne efter deres tilbøjeligheden til overbelastning (den angivne deres kapacitet for overførslen) under den næste uge eller måned. Dette giver mulighed for oprettelse af en kort liste over fordelingsstationer, der skal fungere som input til kortvarig forudsigelse. Som temperaturen er en vigtig indikator for stigning for langsigtede modellen, er der behov for at hele tiden med flere scenarie temperaturen prognoser og feed dem som input til langsigtede modellen. Kort sigt prognosen startes derefter for at forudsige hvilke understation der sandsynligvis overbelastning over næste time.

Kortvarig og langsigtede modeller er installeret individuelt per hver understation. Derfor kræver praktiske udførelse af disse modeller omfattende organisering. Hvis du vil have højere forudsigelse nøjagtighed på kort sigt, er en mere detaljeret model dedikeret for hver timen på dagen. Alle disse modeller udføres hver time og udført udførelse af efter et par minutter at tillade tilstrækkelig tid til at svare og udføre forebyggende handlinger, hvis det er nødvendigt. Denne samling af modeller holdes opdaterede via periodiske omskoling de nyeste data.

Du kan finde flere oplysninger om brug sagen [her](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Brug af store og små bogstaver kvalifikation kriterier – forudsætninger
Den primære styrke af Cortana Intelligence er i dets effektive mulighed for at installere og tilpasse machine learning centreret løsninger. Det er udviklet til at understøtte tusindvis af prognoser, der udføres samtidig. Det kan automatisk skaleres, så det opfylder et ændre forbrug mønster. Der er derfor en løsning fokus på præcision og ydeevne. For eksempel er en utility virksomhed interesseret i at producere nøjagtige energi behov forecast for næste time og for hver timen på dagen. På den anden side er mindre interesseret i at besvare spørgsmålet om, hvorfor behovet er skønsmæssigt beregnet med at være som den er (modellen selve tager sig af,).

Det er derfor vigtigt, at du opdager, at ikke alle use cases, og business problemer kan effektivt løses ved hjælp af machine learning.

Cortana Intelligence og maskine læring kan være meget effektive i løse et problem med angivne business, når følgende kriterier er opfyldt:
-   Business problemet i hånden er **skønnet** i art. Et skønnet Brug tilfælde eksempel er en utility virksomhed, der vil gerne forudsige power belastning på en given understation under næste time. På den anden side ville analysere og rangering drivere af historiske demand være **beskrivende** i art og derfor mindre gælder.
-   Der er en Ryd **stien til handling** skal udføres, når forudsigelse er tilgængelig. For eksempel kan forudsigelser en overbelastning på en understation under næste time udløse en proaktiv handlingen med at reducere belastning, som er knyttet til understation og dermed forhindre en overbelastning potentielt.
-   Use case repræsenterer en **typisk type problemet** Sådan, at når løser det kan føre til løsning af andre lignende use cases.
-   Kunden kan angive **kvantitative og kvalitative mål** til at vise en vellykket løsningsimplementering. For eksempel en god kvantitative mål for energi demand prognose ville være nødvendige nøjagtigheden grænseværdi (*f.eks.*, op til 5% fejl er tilladt) eller når forudsigelser understation overbelastning derefter præcision (rente SAND positive) og tilbagekaldelsen (omfanget af SAND positive) skal være over en given tærskelværdi. Disse mål skal være afledt af kundens virksomhedsmål.
-   Der er et Ryd **integrationsscenarie** med firmaets business arbejdsproces. Understation Indlæs prognosen kan for eksempel integreres i gitteret kommandocentral tillade overbelastning forebyggelse aktiviteter.
-   Er du klar til at bruge **data med tilstrækkelige kvalitet** til at understøtte use case (se mere i næste afsnit, **Datakvaliteten**af denne playbook) kunden.
-   Kunden inddraget skyen centreret data arkitektur eller **skybaseret maskine læ**, herunder Azure ML og andre Cortana Intelligence pakke komponenter.
-   Kunden er villig til at oprette **en start til slut dataflow** pågældende faciliteter leveringen af data i skyen løbende, og parate til **giver muligheder for** løsningen.
-   Kunden er klar til at **allokeres ressourcer** , der indsættes aktivt under den indledende pilot implementering, så viden og ejerskabet af løsningen kan overføres til kunde efter vellykket gennemførelse.
-   Kunde ressourcen skal være en **kvalificeret data professionel**, helst scientist en data.

Kvalifikation af en use case, der er baseret på ovennævnte kriterier kan betydeligt forbedre succes satser for en use case og oprette en god beachhead til gennemførelse af fremtidig brug sager.

### <a name="cloud-based-solutions"></a>Skybaseret løsninger
Cortana Intelligence Suite på Azure er et integreret miljø, der er placeret i skyen. Installation af en avanceret analytics-løsning i et skybaseret miljø indeholder væsentlige fordele for virksomheder og på samme tid kan medføre stor Skift virksomheder, der stadig bruger lokalt IT-løsninger. I energisektoren er der en Ryd tendens gradvist overflytningen af handlinger i skyen. Denne udvikling går hånd i hånd sammen med udvikling af gitteret smart, som beskrevet ovenfor, i **Branchetendenser**. Når denne playbook fokuserer på en skybaseret løsning i energi domæne, er det vigtigt at forklare fordelene og andre overvejelser ved at anvende en skybaseret løsning.

Måske er den største fordel af en skybaseret løsning omkostningerne. Som en løsning gør brug af cloud-installeret komponenter, der er ingen ide omkostninger eller forbrug (omkostninger for solgte varer) komponentomkostninger, der er knyttet til den. Det betyder, at det er ikke nødvendigt at investere i hardware, software og IT vedligeholdelse, og derfor er der en væsentlig reduktion i business risikoen.

En anden vigtige fordel er overkommelige Omkostningsstrukturen i skyen-baserede løsninger. Skybaseret servere til computing eller lagerplads kan installeres og skaleret på grundlag af lige-efter behov. Dette repræsenterer effektivitet omkostninger fordelen ved en skybaseret løsning.

Til sidst, der er ingen grund til investerer i IT vedligeholdelse eller fremtidige infrastrukturudvikling, som alle dette er en del af den skybaseret tilbud. I udstrækning, Cortana Intelligence pakken indeholder bedst i klassen services og bevarer udvikling af dens vejkortet for projektstyring. Nye funktioner, komponenter og -funktioner er konstant introduceres og udvikles.

Efter en virksomhed, der lige er begyndt dens overgang til skyen, vil vi meget anbefale til tage en gradvist fremgangsmåde ved at implementere en skyen overførsel vejkortet for projektstyring. Vi mener, at til værktøjer og firmaer i domænet energi, de use cases, der er beskrevet i denne playbook repræsenterer en glimrende mulighed for pilotprojekter skønnet analytics løsninger i skyen.

#### <a name="business-case-justification-considerations"></a>Overvejelser i forbindelse med arbejdsrelaterede tilfælde justeringen
I mange situationer er kunden måske interesseret i at foretage en business begrundelse for en given use case, hvor en skybaseret løsning og maskine læring er vigtige komponenter. I modsætning til en lokal løsning, hvis det er en skybaseret løsning komponenten ide omkostninger er minimale og de fleste af omkostninger elementerne er tilknyttet faktiske forbrug. Hvis du vil implementere en prognose løsningen på Cortana Intelligence pakke energi, kan flere tjenester integreres med en enkelt almindelige omkostningsstruktur. For eksempel databaser (*f.eks.*, SQL Azure) kan bruges til at gemme den rækkedata, og derefter til de faktiske prognoser Azure ML der bruges til at hoste laver prognose tjenesterne. I dette eksempel kan Omkostningsstrukturen omfatter lagring og transaktions komponenter.

På den anden side har en en god forståelse af forretningsværdi af operativsystem en behovet for energi prognoser (kort eller lang sigt). Faktisk er det vigtigt at huske forretningsværdi for hver enkelt prognose operation. For eksempel præcist budgettering power indlæses for de næste 24 timer kan forhindre overproduction eller kan forhindre overloads i gitteret og dette kan angives med hensyn til finansielle sparer dagligt.

En grundlæggende formel til beregning af finansielle fordelen demand forecast-løsning ville være: ![grundlæggende formel til beregning af finansielle fordelen demand forecast-løsning](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Da Cortana Intelligence pakken indeholder en overkommelige priser model, er der ingen grund til at foretage en fast omkostningskomponent til denne formel. Denne formel kan beregnes på basis af hver dag, månedligt eller årligt.

Aktuelle Cortana Intelligence pakke og Azure ML priser planer kan findes [her](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Processen til udvikling af løsningen
Udvikling livscyklussen for en prognose løsning omfatter typisk 4 faser, som alle vi yder behovet for energi brug af skybaseret teknologier og tjenester i den Cortana Intelligence pakke.

Dette er vist i følgende diagram:

![Smart gitter cyklus](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Det følgende afsnit beskrives denne proces i 4 trin:

1.  **Dataindsamling** – en hvilken som helst avanceret analyse baseret løsning er baseret på data (se **Data om**). Specifikt, når det drejer sig om skønnet analyser og prognoser, vi er afhængige af igangværende, dynamiske strømmen af data. I forbindelse med energi demand prognoser, disse data kan købes direkte fra smart målere, eller aggregeres allerede på et lokalt database. Vi er også afhængige af andre eksterne datakilder som vejrudsigt og temperaturen. Dette igangværende flow af data skal orchestrated, planlagt og gemt. [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) er vores primære arbejdshesten for dig med at udføre denne opgave.
2.  **Modeling** – For nøjagtige og pålidelige energi forecasts, en skal udvikle (tog) og vedligeholde en god model, gør brug af de historiske data og henter beskrivende og skønnet mønstre i dataene. Området af Machine Learning (ML) er vokser hurtigt med mere avancerede algoritmer, der udvikles regelmæssigt. Azure ML Studio indeholder en stor brugeroplevelse, der hjælper med at bruge de mest avancerede ML algoritmer i en komplet arbejdsgang. Arbejdsprocessen er vist i en intuitiv rutediagram og omfatter forberedelse af data, udtrækning af funktion, modellering og beregning i modellen. Brugeren kan trække i hundredvis af forskellige modeller, der er inkluderet i dette miljø. Ved slutningen af denne fase har en data scientist en arbejdsmodel, der er fuldt evalueret og klar til installation.

    I det følgende diagram er en illustration af en typisk arbejdsproces:

    ![Modellering arbejdsproces](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)

3.  **Installation** – med en arbejdsmodel i hånd, er næste trin er installation. Her konverteres modellen til en webtjeneste, der viser et RESTful API, der kan aktiveres samtidig fra forskellige forbrug klienter over internettet. Azure ML indeholder en enkelt metode til at implementere en model direkte fra Azure ML Studio med et enkelt klik på en knap. Hele installationsprocessen sker der under struktur. Denne løsning kan automatisk skaleres, så det opfylder de nødvendige forbrug.

4.  **Forbrug** – i denne fase skal vi faktisk gøre brug af laver prognose modellen til at producere prognoser. Forbrug kan styres fra en brugerprogram (*f.eks.*, dashboard) eller direkte fra et funktionsdygtige system som demand/levering justering af system eller en løsning til optimering af gitteret. Flere Brug tilfælde kan styres fra en enkelt model.

## <a name="data-understanding"></a>Forstå data
Efter dækker business overvejelser (se **Business om**) af en prognose løsning behovet for energi, er vi nu klar til at diskutere delen data. En hvilken som helst skønnet analytics-løsning er baseret på pålidelige data. Til energi demand prognose, vi er afhængige af historiske forbrugsdata med forskellige granularitetsniveauer. Disse historiske data bruges som råvaren. Det skal igennem en forsigtig analyse, hvor data scientist identificerer predictors (også kaldet funktioner), der kan anbringes i en model, som til sidst genererer kræves af prognoser.

I resten af dette afsnit, som vi beskriver de forskellige trin og overvejelser i forbindelse med at forstå dataene og hvordan du kan finde den til en anvendelig formular.

### <a name="the-model-development-cycle"></a>Model udviklingscyklus
Producerer god prognoser modeller kræver nogle omhyggelige behandling og planlægning. Opdele modelleringsprocessen i flere trin og fokusere på ét trin ad gangen, kan du dramatisk forbedre med resultatet af hele processen.

I følgende diagram vises, hvordan modelleringsprocessen kan opdeles i flere trin:

![Model udviklingscyklus](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Som kan ses cyklussen består af seks trin:
-   Problem udformning
-   Data indtagelse og udforske data
-   Forberedelse af data og funktion teknisk
-   Modellering
-   Model beregning
-   Udvikling

I resten af dette afsnit beskriver vi de enkelte trin og elementer skal du tænke på hvert trin.

### <a name="problem-formulation"></a>Problem udformning
Vi kan overveje at problemet udarbejdelse som de vigtigste trin i en skal tage før implementering af enhver skønnet analytics-løsning. Her vi vil transformere business problemet og opdele til bestemte elementer, som kan løses ved hjælp af data og -modellering teknikker. Det er en god ide at beskrive problemet som et sæt vi vil gerne besvare spørgsmål. Her er nogle mulige spørgsmål, der kan være relevante er omfattet af energi demand prognoser:
-   Hvad er den forventede belastning på en enkelt understation i den næste time eller dag?
-   På hvilke tid på dagen påvirkes min gitter spidsbelastning behov?
-   Hvor sandsynligt er min gitter til at opretholde forventede spidsbelastning?
-   Hvor meget power skal kraftværker generere i hver time dagens?

Udarbejdelsen disse spørgsmål giver mulighed at fokusere på få de rigtige data og implementere en løsning, der er fuldt justeret business problemet ved hånden. Desuden kan du derefter angive nogle vigtige mål, som gør det muligt at evaluere ydeevnen for modellen. For eksempel, hvor nøjagtig prognosen skal, og hvad er celleområde, fejl, der stadig er acceptable ved virksomheden?

### <a name="data-sources"></a>Datakilder
Moderne smart gitteret indsamler data fra forskellige webdele og -komponenter i gitteret. Disse data repræsenterer forskellige aspekter af handlingen og anvendelsen af gitteret power. Vi er at begrænse diskussionen på datakilder, så de afspejler det faktiske behov forbrug er omfattet af behovet for energi forecast. En vigtig kilde strømforbruget er smart målere. Værktøjer hele verden installerer hurtigt smart målere til deres forbrugere. Smart målere optage faktisk strømforbruget og konstant relay disse data tilbage til utility firmaet. Data indsamles og sendes tilbage med faste intervaller, lige fra hver 5 minutter til 1 time. Mere avancerede smart målere kan programmeres fjernadgang til at styre og saldo det faktiske forbrug inden for en husstand. Smart dokumentmåler data er relativt stabil og omfatter et tidsstempel. Det gør det en vigtige datablad for behov forecast. Dokumentmåler data kan være sammenlagt (summerede op) på forskellige niveauer i gitteret topologien: transformer, understation, region, *osv*. Vi kan derefter vælge det nødvendige sammenlægning niveau til at opbygge en laver prognose model for den. Eksempelvis hvis utility firmaet ønsker, at forudsige fremtidige belastning på hver af dens gitter fordelingsstationer kan derefter alle målere data aggregeret for hver enkelt understation og bruges som input til laver prognose modellen. Vi refererer til smart målere som en intern datakilde.

En pålidelig energi demand prognose er også afhængige af andre eksterne datakilder. En vigtig faktor, der påvirker strømforbruget er vejret, eller mere præcist temperaturen. Historiske data viser stærke korrelation mellem eksterne temperaturen og strømforbruget. Under varm sommer dage forbrugere gøre brug af deres klimaanlæg og under vinter power on opvarmning systemer. En pålidelig kilde af historiske temperaturer på gitter placeringen er derfor nøgle. Desuden vi også er afhængige af nøjagtigt forecast af temperaturen som en indikator for stigning i strømforbruget.

Andre eksterne datakilder kan også hjælpe med at opbygge energi demand prognose modeller. Disse kan indeholde langtidsaftale såfremt ændringer, økonomisk indeks (*f.eks.*, BNP) og andre. I dette dokument indeholder vi ikke disse andre datakilder.

### <a name="data-structure"></a>Datastruktur
Når du identificerer de nødvendige datakilder, vil vi gerne sikre, at rækkedata, der indsamles, har de korrekte datafunktioner. For at opbygge en pålidelig demand prognose model, vil vi brug at sikre, at de data, der indsamles omfatter dataelementer, der kan hjælpe med at forudsige fremtidige behov. Her er nogle grundlæggende krav til den rækkedata datastruktur (skema).

Den rækkedata består af rækker og kolonner. Hver måling repræsenteres som en enkelt række med data. Hver række med data indeholder flere kolonner (også kaldet funktioner eller felter).

1.  **Tidsstempel** – tidsstempelfeltet repræsenterer den faktiske tid, når en måling, der blev registreret. Det skal overholde en af de almindelige formater for dato og klokkeslæt. Både dato og klokkeslæt dele der skal medtages. I de fleste tilfælde er der ingen grund til tid til at være optaget, indtil det andet niveau i granularitet. Det er vigtigt at angive den tidszone, hvor dataene er optaget.
2.  **Tæller-ID** - feltet identificerer tælleren eller måling enhed. Det er en kategoriske variabel og kan være en kombination af cifre og tegn.
3.  **Forbrug værdi** – dette er det faktiske forbrug på en given dato og klokkeslæt. Forbrug kan måles i kWh (kilowatt-hour) eller andre foretrukne enheder. Det er vigtigt at være opmærksom på, at måleenheden skal være ensartede på tværs af alle målinger i dataene. I nogle tilfælde kan forbrug leveres over 3 power faser. I dette tilfælde skal vi at indsamle alle uafhængige forbrug faser.
4.  **Temperaturen** – temperaturen indsamles typisk fra en uafhængig kilde. Det skal dog være kompatible med dataene, forbrug. Den skal indeholde et tidsstempel som beskrevet ovenfor, der gør det til at blive synkroniseret med de faktiske forbrugsdata. Værdien temperaturen kan angives i grader Celsius eller Fahrenheit, men skal være ensartede på tværs af alle målinger.
5.  **Placering –** Placeringsfeltet er typisk knyttet til det sted, hvor temperaturen dataene er blevet indsamlet. Det kan gengives som et postnummer tal eller i længde-/ breddegrader (breddegrad) format.

De følgende tabeller viser eksempler på en god forbrug og temperaturen dataformat:

|**Dato**|**Tid**|**Tæller-ID**|**Fase 1**|**Fase 2**|**Fase 3**|
|--------|--------|------------|-----------|-----------|-----------|
|7-1-2015|10:00:00|ABC1234     |7.0        |2.1        |5.3        |
|7-1-2015|10:00:01|ABC1234     |7.1        |2.2        |4,3        |
|7-1-2015|10:00:02|ABC1234     |6.0        |2.1        |4.0        |

|**Dato**|**Tid**|**Placering**|**Temperaturen**|
|--------|--------|-------------|---------------|
|7-1-2015|10:00:00|11242        |24.4           |
|7-1-2015|10:00:01|11242        |24.4           |
|7-1-2015|10:00:02|11242        |24,5           |

Som kan ses ovenfor, indeholder i dette eksempel 3 forskellige værdier for forbrug, der er knyttet til 3 power faser. Bemærk også, at felterne dato og klokkeslæt er adskilt, men de kan også kombineres til en enkelt kolonne. I dette tilfælde er kolonnen placering repræsenteret i formatet 5-cifret postnummer og temperaturen i formatet grad Celsius.

### <a name="data-format"></a>Dataformat
Cortana Intelligence pakke kan understøtte de mest almindelige dataformater som *csv-, TSV, JSON osv*. Det er vigtigt, at dataformatet forbliver ensartet for hele livscyklussen for projektet.

### <a name="data-ingestion"></a>Data indtagelse
Da energi demand prognose er konstant og ofte forventede, skal vi sikre dig, at den rækkedata der flyder ved hjælp af en dækkende og pålidelige data indtagelse proces. Processen indtagelse skal sikre, at den rækkedata er tilgængelig for processen laver prognose på påkrævet tid. Der medfører, at data indtagelse hyppigheden skal være større end laver prognose hyppigheden.

For eksempel: Hvis vores demand prognoser løsning vil oprette en ny prognose på 8:00 AM dagligt derefter vi brug at sikre, at alle de data, der er indsamlet under de seneste 24 timer er blevet fuldt optagelse indtil dette punkt og skal lige indeholder den seneste time af data.

For at gøre dette, indeholder Cortana Intelligence pakke forskellige måder til at understøtte en pålidelige data indtagelse proces. Dette vil yderligere beskrevet i afsnittet **installation** af dette dokument.

### <a name="data-quality"></a>Datakvalitet
Rækkedata kilden, som kræves for at udføre stabil og præcise behov prognoser skal opfylde kvalitetskriterier nogle grundlæggende data. Men avancerede statistiske metoder kan bruges til kompensation for nogle mulige data lydkvaliteten problem, vi stadig har brug at sikre, at vi krydser grænseværdi nogle grundlæggende data for kvalitet, når ingesting nye data. Her er nogle overvejelser vedrørende rækkedata kvalitet:
-   **Manglende værdi** – dette henviser til situationen, når bestemte mål ikke er indsamlet. Det grundlæggende krav her er, at den manglende værdi rente ikke skal være større end 10% for en given periode. I tilfælde, mangler der en enkelt værdi, den skal være angivet ved hjælp af en foruddefineret værdi (for eksempel: '9999') og ikke '0', som kan være en gyldig måleenhed.
-   **Mål nøjagtigheden** – den faktiske værdi for forbrug eller temperaturen bør registreres præcist. Forkerte mål vil give forkerte prognoser. Typisk skal fejlen mål være mindre end 1% i forhold til værdien SAND.
-   **Under måling** – det er nødvendigt, faktisk tidsstemplet data indsamles der ikke afviger med mere end 10 sekunder i forhold til sand klokkeslættet for det faktiske mål.
-   **Synkronisering** – når der bruges flere datakilder (*f.eks.*, forbrug og temperaturen) vi skal sikre, at der er ingen tidssynkronisering problemer mellem dem. Det betyder, at tidsforskellen mellem indsamlede tidsstemplet fra en hvilken som helst to uafhængige datakilder ikke må overstige mere end 10 sekunder.
-   **Ventetid** - som beskrevet ovenfor, i **Data indtagelse**vi er afhængige af en pålidelige data flow og indtagelse proces. Styre, der skal vi sikre, at vi styre data ventetid. Dette er angivet som tidsforskellen mellem den tid, der blev foretaget det faktiske mål og det klokkeslæt, hvor det er blevet indlæst i Cortana Intelligence pakke opbevaring og er klar til brug. For kort sigt Indlæs prognoser samlede ventetid skal ikke være større end 30 minutter. For langtidsaftale belastning prognoser samlede ventetid må ikke være større end 1 dag.

### <a name="data-preparation-and-feature-engineering"></a>Forberedelse af data og funktion teknisk
Når den rækkedata er oralt (se **Data indtagelse**) og sikkert er blevet gemt, er du klar til at blive behandlet. Data forberedelse fasen som at tage den rækkedata og konvertering (transformerer, omforme) dem til en formular til fasen modellering. Der kan omfatter enkle handlinger som ved hjælp af kolonnen rækkedata, som er med dens faktiske værdi er målt, standardiseret værdier, mere komplekse handlinger som [tid langsom](https://en.wikipedia.org/wiki/Lag_operator)og andre. De nyoprettede datakolonner der henvises til som datafunktioner, og processen til oprettelse af disse kaldes funktion teknisk. Vi ville have et nyt datasæt, der er blevet udledt fra den rækkedata og kan bruges til modellering i slutningen af denne proces. Data forberedelse fasen skal desuden tager sig af manglende værdier (se **Datakvalitet**) og kompensation for dem. I nogle tilfælde skal vi også normalisere data for at sikre, at alle værdier, der er repræsenteret i det samme målestoksforhold.

I dette afsnit, vi oversigt over nogle af de almindelige datafunktioner, der er inkluderet i energien forecast demand modeller.

**Tid indsatsbaserede funktioner:** Disse funktioner er udledt fra datatypen dato/tidsstempel. Disse er hentet og konverteret til kategoriske funktioner som:
-   Tid på dagen – dette er timen på dagen som tager værdier fra 0 til 23
-   Dag i ugen – dette repræsenterer dagen i ugen og tager værdier fra 1 (søndag) til 7 (lørdag)
-   Dag i måneden – dette repræsenterer den faktiske dato og kan tage værdier fra 1 til 31
-   Måned i året – dette repræsenterer måneden og tager værdier fra 1 (januar) til 12 (December)
-   Weekend – dette er en funktion, binær værdi, der tager værdierne 0 for ugedage eller 1 til weekend
-   Ferie – dette er en funktion, binær værdi, der tager værdierne 0 til en almindelig dag eller 1 til en fridag
-   Fourier vilkår – Fourier betingelserne er vægt, der er afledt tidsstemplet og bruges til at registrere sæsonudsving (skifter) i dataene. Da vi kan have flere årstider i vores data skal vi muligvis flere Fourier udtryk. For eksempel demand værdier skal muligvis årlige, uge eller daglige årstider/skifter som medfører 3 Fourier udtryk.

**Uafhængige mål funktioner:** Uafhængige funktioner omfatter alle de dataelementer, som vi vil gerne bruge som predictors i vores model. Her kan vi udelade funktionen afhængige som vi har brug for til at forudsige.
-   Mellemliggende funktion – dette er tid, der er forskudt mod værdierne i det faktiske behov. For eksempel skal mellemliggende 1 funktioner indeholde demand værdien i den forrige time (hvis hver time data) i forhold til det aktuelle tidsstempel. På samme måde, kan vi tilføje mellemliggende 2, mellemliggende 3, *osv*. Den faktiske kombination af mellemliggende funktioner, der bruges, bestemmes i fasen modellering af evaluering af model resultaterne.
-   Sigt lang trending – denne funktion repræsenterer lineær væksten i demand mellem år.

**Afhængige funktion:** Funktionen afhængige er den datakolonne, ønsker vi vores model til at forudsige. Med [tilsyn maskine læ](https://en.wikipedia.org/wiki/Supervised_learning), har vi brug for til at undervise først modellen ved hjælp af funktionerne afhængige (hvilket også kaldes etiketter). Dette giver mulighed for modellen for at lære mønstrene i de data, der er knyttet til funktionen afhængige. Energi behov forecast vi typisk vil forudsige det faktiske behov, og derfor skal vi bruge den som funktionen afhængige.

**Håndtering af manglende værdier:** I fasen data forberedelse har vi brug for til at bestemme den bedste strategi til at håndtere manglende værdier. Dette gøres hovedsageligt ved hjælp af de forskellige statistiske [data fradrag metoder](https://en.wikipedia.org/wiki/Imputation_(statistics)). Hvis det er energi demand prognoser impute vi typisk manglende værdier ved hjælp af glidende gennemsnit fra forrige tilgængelige datapunkter.

**Datanormalisering:** Datanormalisering er en anden type transformation, som bruges til at hente alle numeriske data som behov forecast til en lignende skala. Dette med typisk til at forbedre nøjagtigheden af modellen og præcision. Vi vil plejer at gøre dette ved at dividere den faktiske værdi med dataområdet.
Dette vil skalere den oprindelige værdi i et mindre interval, typisk mellem 1 og 1.

## <a name="modeling"></a>Modellering
Modellering fasen er, hvor konvertering af dataene i en model finder sted. I kernen i denne proces, der er avancerede algoritmer, der scanne de historiske data (kursus data), udtrække mønstre og oprette en model. Modellen kan bruges senere til at forudsige på nye data, der ikke er blevet brugt til at oprette modellen.

Når vi har en stabil arbejdsmodel kan vi bruge det til at give nye data, der er struktureret for at medtage de nødvendige funktioner (X). Vurdering processen gør brug af permanente modellen (objektet fra fasen oplæring) og forudsige den destinationsadresse-variabel, som er markeret med Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Demand prognoser Modeling teknikker
I forbindelse med demand prognoser vi yder brug af historiske data, der er sorteret efter tid. Vi generelt referere til data, der indeholder tidsdimensionen som [tidsserie](https://en.wikipedia.org/wiki/Time_series). Mål i tid serie modellering er at finde tid relaterede tendenser, sæsonudsving, automatisk-korrelations (korrelations over tid), og afgive dem i en datamodel.

I de seneste år er avancerede algoritmer udviklet til at tage højde for tidsserier og forbedre laver prognose nøjagtigheden. Gennemgås kortvarigt nogle af dem her.

> [AZURE.NOTE] Dette afsnit er ikke beregnet skal bruges som en maskine, undervisning og prognoser oversigt, men hellere som en kort oversigt over modeling teknikker, der ofte bruges til demand prognose. Flere oplysninger og informative materiale om tidsserier, anbefales det online bogen [Budgettering: principper og øvelse](https://www.otexts.org/book/fpp).

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**Glidende gennemsnit (glidende gennemsnit)**](https://www.otexts.org/fpp/6/2)
Glidende gennemsnit er en af de første analytical teknikker, der er blevet brugt for tidsserier og den er stadig en af mest almindeligt brugte teknikker fra og med dags dato. Det er også grundlaget for mere avancerede prognoser teknikker. Med glidende gennemsnit vi prognoser det næste datapunkt ved at tage gennemsnittet over de seneste K-punkter, hvor K angiver rækkefølgen af det glidende gennemsnit.

Glidende gennemsnit metode har effekten af udjævning prognosen og kan derfor ikke håndtere godt store flygtighed i dataene.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (eksponentiel udglatning)**](https://www.otexts.org/fpp/7/5)
Eksponentiel udglatning (ETS) er en række forskellige metoder, bruger vægtet gennemsnit seneste datapunkter for at forudsige det næste datapunkt. Ideen er til at tildele højere tykkelser til nyere værdier og gradvist mindske denne vægt for ældre målt værdier. Der er en række forskellige metoder med denne familie, nogle af dem omfatter håndtering af sæsonudsving i dataene som [Holt Winters sæsonbestemte metode](https://www.otexts.org/fpp/7/5).

Nogle af disse metoder også angive sæsonudsvingene til dataene.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (automatisk Regression integreret flytte gennemsnit)**](https://www.otexts.org/fpp/8)
Automatisk Regression integreret flytte MIDDEL (ARIMA) er en anden familie af metoder, er ofte anvendte tidsserie prognoser. Det kombinerer næsten automatisk regression metoder med glidende gennemsnit. Automatisk regression metoder bruge regression modeller ved at tage forrige serie klokkeslætsværdier for at beregne det næste datopunkt. ARIMA metoder også anvende forskellige metoder, der omfatter at beregne forskellen mellem datapunkter og bruge dem i stedet for den oprindelige målte værdi. Til sidst skal ARIMA også gør brug af de glidende gennemsnit teknikker, der er beskrevet ovenfor. Kombinationen af alle af følgende metoder på forskellige måder er, hvad konstruktioner familie af ARIMA metoder.

ETS og ARIMA anvendes bredt i dag til energi demand prognoser og mange andre laver prognose problemer. I mange tilfælde disse kombineres sammen til at levere meget præcise resultater.

**Generelt multiplum Regression** Regression modeller kan være den vigtigste modellering tilgang i domænet for maskine læring og statistik. I forbindelse med tidsserie bruge vi regression for at forudsige fremtidige værdier (*f.eks.*, af demand). I simple lineære regression vi tage en lineær kombination af predictors og få mere at vide vægt (også kaldet koefficienter) af disse predictors under processen kursus. Målet er at frembringe en regressionslinje, som bliver budget vores estimerede værdier. Regression metoder er egnet, når variablen mål er numeriske, og derfor også passer tidsserier. Der er en lang række regression metoder, herunder meget simpel regression modeller som [Lineær Regression](https://en.wikipedia.org/wiki/Linear_regression) og mere avancerede dem som beslutningstræer, [Tilfældig områder](https://en.wikipedia.org/wiki/Random_forest), [Neural netværk](https://en.wikipedia.org/wiki/Artificial_neural_network)og øget beslutningstræer.

Bygning af behovet for energi prognoser regression problemer giver os en masse fleksibilitet ved at vælge vores datafunktioner, der kan kombineres fra de faktiske behov tid seriedata og eksterne faktorer som temperaturen. Du kan finde flere oplysninger om de valgte funktioner er beskrevet i funktionen Engineering (se **forberedelse af Data og funktion Engineering**) sektion af denne playbook.

Fra vores erfaring med implementering og installation af energi demand prognoser pilotprojekt, vi har fundet, at de avancerede regression modeller, der er tilgængelige i Azure ML har tendens til at giver de bedste resultater, og vi gør brug af dem.

## <a name="model-evaluation"></a>Model beregning
Model evaluering har en vigtig rolle i den **Model udviklingscyklus**. Vi ser til validering af modellen og dens ydeevne med virkelige liv data på dette trin. Under trinnet modellering bruge vi en del af de tilgængelige data til uddannelse modellen. I evalueringsfasen tager vi resten af dataene for at teste modellen. Næsten betyder det, at vi fødning af de nye data til model, der er blevet omstruktureret og indeholder de samme funktioner som kursus datasættet. Under processen datavalidering, skal vi dog bruge modellen til at forudsige variablen mål i stedet for at give variablen tilgængelige mål. Vi refererer ofte til denne proces som model for vundne. Vi vil derefter bruger SAND målværdier og sammenligne dem med estimerede dem. Målet her er at måle og minimere fejlen forudsigelse, hvilket betyder, at forskellen mellem prognoser og værdien SAND. Kvantificering fejl mål er nøgle, da vi vil gerne finjustere modellen og kontrollere, om fejlen er faktisk faldende. Finjustere modellen kan gøres ved at ændre model parametre, der styrer processen learning eller ved at tilføje eller fjerne datafunktioner (kaldes [parametre oprydning](https://channel9.msdn.com/Blogs/Windows-Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Stort set det betyder, at vi kan være nødvendigt at navigere mellem funktion teknisk, modeling, og modellere evaluering faser flere gange, indtil vi er i stand til at reducere fejlen til det ønskede niveau.

Det er vigtigt at fremhæve, fejlen forudsigelse aldrig være nul som der er aldrig en model, der kan perfekt forudsige hver resultatet. Der er dog en bestemt omfanget af fejl, der kan accepteres af virksomheden. Under valideringsprocessen ønsker vi at sikre, at vores model forudsigelse fejl på niveauet for eller er bedre end business toleranceniveau. Det er derfor vigtigt at angive niveauet for den tolerance i starten af cyklus i fasen **Problem udformning** .

### <a name="typical-evaluation-techniques"></a>Typisk evalueringsteknikker
Der er forskellige måder, i hvilken forudsigelse fejl kan målt og angivelse af tal. I dette afsnit fokuserer vi diskussion på evalueringsteknikker relevante til tidsserie og i specifikke for energi behov forecast.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE står for betyder absolutte procentvise fejl. Med MAPE vi computing forskellen mellem hver forecast punkt og den faktiske værdi for dette punkt. Vi derefter sæt tal på fejlen per punkt ved at beregne andelen af forskellen i forhold til den faktiske værdi. På det sidste trin gennemsnitlig vi disse værdier. Den matematiske formel, der bruges til MAPE er følgende:

![MAPE formel](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*hvor<sub>t</sub> er den faktiske værdi, F,<sub>t</sub> er de estimerede værdier, og n er prognose forude.*

## <a name="deployment"></a>Installation
Når vi har faste ned fasen modellering og godkendt model ydeevnen er vi klar til at gå til installationsfasen. I denne sammenhæng betyder installation, så kunden for at bruge modellen ved at køre faktisk prognoser på den i stor skala. Begrebet installation er nøgle i Azure ML, da vores primære mål er at kalde konstant prognoser i modsætning til kun at få indsigt fra dataene. Installationsfasen er del, hvor vi aktivere modellen at blive brugt på stor skala.

I forbindelse med energi demand prognose er vores mål at kalde fortløbende og periodiske prognoser og sikre, at nye oplysninger er tilgængelig for modellen og at de forventede data sendes tilbage til arket klienten.

### <a name="web-services-deployment"></a>Web Services installation
Primære installeres dokumentkomponenten i Azure ML er webtjenesten. Dette er den mest effektive måde at aktivere forbrug af en skønnet model i skyen. Webtjenesten indkapsler modellen og ombrydes den med en [RESTful](http://www.restapitutorial.com/) API (Application Programming Interface). API kan bruges som en del af en hvilken som helst klient kode som vist i diagrammet nedenfor.

![Vi Service installation og forbrug](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Som kan ses, webtjenesten er implementeret i skyen Cortana Intelligence pakke og kan aktiveres over dokumentets udsatte REST-API slutpunkt. Forskellige typer klienter på tværs af forskellige domæner kan aktivere tjenesten via Web API samtidigt. Webtjenesten kan også tilpasse for at understøtte tusindvis af samtidige opkald.

### <a name="a-typical-solution-architecture"></a>En typisk løsningsarkitektur
Når du installerer en prognose løsning behovet for energi, er interesseret i at implementere en til en komplet løsning, der går ud over webtjenesten forudsigelse og letter hele dataflow. På det tidspunkt, vi kalde en ny prognose, vil vi brug at sikre dig, at modellen indføres med de opdaterede datafunktioner. Der indebærer, at den nyligt indsamlede rækkedata er konstant optagelse, behandles, og omdannet til funktionen kræves på modellen blev oprettet. På samme tid, vil vi gerne gør den forventede data tilgængelige til slutningen forbrug klienter. Et eksempel på data flow cyklus (eller data pipeline) illustreres i diagrammet nedenfor:

![Behovet for energi Forecast start til slut dataflow](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Dette er de trin, som finder sted som en del af energi demand prognose cyklus:
1.  Millioner af udløst data målere genererer konstant power forbrugsdata i realtid.
2.  Disse data der indsamles, og der er overført til en skybaseret lager (*f.eks.*, Azure Blob).
3.  Før behandles, sammenlægges den rækkedata til en understation eller regionalt plan, som defineret i virksomheden.
4.  Funktionen behandling (se **forberedelse af Data og behandling af funktion**) derefter finder sted og giver de data, der kræves til model kursus eller vundne-funktion sæt data er gemt i en database (*f.eks.*, SQL Azure).
5.  Tjenesten igen kurser startes til at undervise igen laver prognose modellen – den opdaterede version af modellen bevares, så den kan bruges af vurdering webtjenesten.
6.  Vurdering webtjenesten startes på en tidsplan, der passer til den nødvendige prognose hyppighed.
7.  De forventede data er gemt i en database, der kan åbnes af slutningen forbrug klienten.
8.  Forbrug klienten henter prognoser, gælder den tilbage til gitteret eller forbruger i overensstemmelse med den nødvendige use case.

Det er vigtigt at være opmærksom på, at denne hele cyklus er fuldautomatisk og kører på en tidsplan. Hele organiseringen af denne data cyklus kan udføres ved hjælp af funktioner som [Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Start til slut installationsarkitektur
For at kunne installere næsten løsning på Cortana Intelligence en energi demand prognose, har vi brug at sikre, at de påkrævede komponenter er oprettet og konfigureret korrekt.

I følgende diagram vises et typisk Cortana Intelligence baseret arkitektur, der implementerer og orchestrates data flow cyklussen, som er beskrevet ovenfor:

![Start til slut installationsarkitektur](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Se skabelonen energi løsning du kan finde flere oplysninger om hvert af komponenterne og hele arkitekturen.
