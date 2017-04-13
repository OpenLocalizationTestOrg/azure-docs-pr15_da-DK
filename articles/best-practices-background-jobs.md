<properties
   pageTitle="Baggrund job vejledning | Microsoft Azure"
   description="Vejledning i baggrunden opgaver, der Kør uafhængigt af brugergrænsefladen."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="masashin"/>

# <a name="background-jobs-guidance"></a>Baggrund job vejledning

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Oversigt

Mange typer programmer kræver baggrundsopgaver, der kører uafhængigt af brugergrænsefladen (UI). Som eksempler kan nævnes batchjob, intensivt behandlingsopgaver og længerevarende processer som arbejdsprocesser. Baggrundsjob kan udføres uden brugerinput – programmet kan starte jobbet og derefter fortsætte med at behandle interaktive anmodninger fra brugere. Dette kan hjælpe med at minimere belastning på programmet brugergrænseflade, som kan forbedre tilgængelighed og reducere interaktive svar gange.

F.eks, hvis et program er påkrævet til at generere miniaturer af billeder, der overføres af brugere, kan det gøre det som et baggrundsjob med og gemme miniaturen til lager, når den er fuldført – uden at brugeren, der er brug at vente processen udføres. En bruger, placere en rækkefølge kan starte en arbejdsproces for baggrund, der behandler ordren, mens Brugergrænsefladen giver brugeren mulighed at fortsætte med at gennemse WebApp på samme måde. Når baggrunden jobbet er fuldført, kan det opdatere de lagrede ordrer data og sende en mail til den bruger, der bekræfter rækkefølgen.

Når du overveje, om at implementere en opgave som et baggrundsjob, de primære kriterier er om opgaven kan køre uden brugerinput og uden Brugergrænsefladen skulle vente et job skal udføres. Opgaver, der kræver brugeren eller Brugergrænsefladen vente lidt, mens de er færdige muligvis ikke relevante som baggrundsjob.

## <a name="types-of-background-jobs"></a>Typer af baggrundsjob

Baggrundsjob omfatter typisk en eller flere af følgende typer job:

- CPU-intensivt job som matematiske beregninger eller strukturel model analyse.
- Jeg/O-intensivt job, som udfører en række lagerplads transaktioner eller indeksering af filer.
- Batchjob som natlig dataopdateringer eller planlagte behandling.
- Længerevarende arbejdsprocesser, som ordren kan opfyldes eller klargøring af tjenester og -systemer.
- Følsomme data behandling hvor opgaven er afleveret til en mere sikker placering til behandling. Du kan eksempelvis ikke vil til at behandle følsomme data i en web-app. I stedet kan du bruge et mønster som [Gatekeeper](http://msdn.microsoft.com/library/dn589793.aspx) til at overføre data til en isolerede i baggrunden, der har adgang til beskyttet lager.

## <a name="triggers"></a>Udløsere

Baggrundsjob kan startes på flere forskellige måder. De falder inden for en af følgende kategorier:

- [**Event-driven udløsere**](#event-driven-triggers). Opgaven startes som svar på en begivenhed, typisk en handling, som en bruger eller et trin i en arbejdsproces.
- [**Tidsplan-driven udløsere**](#schedule-driven-triggers). Opgaven startes på en tidsplan baseret på en timer. Det kan være en tilbagevendende tidsplan eller en manuel start, der er angivet for et senere tidspunkt.

### <a name="event-driven-triggers"></a>Hændelsesstyret udløsere

Hændelsesstyret aktivering bruger en udløser til at starte opgaven baggrund. Eksempler på brug af hændelsesstyret udløsere omfatter:

- Brugergrænsefladen eller et andet job du placerer en meddelelse i en kø. Meddelelsen indeholder data om en handling, der er sket, som brugeren placere en rækkefølge. Opgaven baggrund lytter på denne kø og registrerer modtagelse af en ny meddelelse. Den læser meddelelsen og bruger dataene i den som input til jobbet baggrund.
- Brugergrænsefladen eller et andet job gemmer eller opdaterer en værdi i lagerplads. Opgaven baggrund overvåger opbevaring og registrerer ændringer. Den læser data, der bruges som input til jobbet baggrund.
- Brugergrænsefladen eller et andet job, bliver en anmodning for et slutpunkt, som en HTTPS URI eller en API, der vises som en webtjeneste. Den overfører de data, der kræves for at fuldføre opgaven baggrunden som en del af anmodningen. Tjenesten slutpunktet eller web aktiverer opgaven baggrund, som bruger dataene som input.

Typiske eksempler på opgaver, som egner sig til hændelsesstyret aktivering medtage billede behandling, arbejdsprocesser, sender oplysninger til eksterne tjenester, sende e-mail-meddelelser og klargøring af nye brugere i multiprofiler programmer.

### <a name="schedule-driven-triggers"></a>Tidsplan-driven udløsere

Tidsplan-driven aktivering bruger en timer for at starte baggrund opgaven. Eksempler på brug af tidsplan-driven udløsere omfatter:

- En timer, der kører lokalt i programmet eller som en del af programmets operativsystem kalder en baggrund opgave med jævne mellemrum.
- En timer, der kører i et andet program eller en timer-tjeneste, såsom Azure Scheduler, sender en anmodning til en API eller web tjeneste med jævne mellemrum. Tjenesten API eller web aktiverer opgaven baggrund.
- Starter en timer, som får baggrund opgaven skal aktiveres én gang efter en bestemt tidsforsinkelse eller på et bestemt tidspunkt, en separat proces eller et program.

Typisk eksempler opgaver, som egner sig til aktivering for tidsplan-driven på batchbehandling rutiner (såsom opdatering af relaterede produkter lister for brugere, der er baseret på deres seneste funktionalitet), rutinemæssige databehandling opgaver (som opdatering af indeks eller generere akkumulerede resultater), dataanalyse til daglige rapporter, data opbevaring Oprydning og data konsistens trykkeri.

Hvis du bruger en tidsplan indsatsbaseret opgave, der skal køre som en enkelt forekomst skal være opmærksom på følgende:

- Hvis den Beregn forekomst, der kører planlæggeren (som en virtuel maskine, ved hjælp af Windows planlagte opgaver) skaleres, får du flere forekomster af planlæggeren kører. Disse kan starte flere forekomster af opgaven.
- Hvis opgaver kører længere end perioden mellem scheduler begivenheder, kan planlæggeren starte en anden forekomst af opgaven, mens den forrige opgave stadig er aktiv.

## <a name="returning-results"></a>Returnerer resultater

Baggrundsjob udføres asynkront i en separat proces eller endda i et andet sted end Brugergrænsefladen eller den proces, der startede opgaven baggrund. Ideelt set baggrundsopgaver er "brand og glemmer" handlinger, og deres status for udførelse af har ingen indvirkning på Brugergrænsefladen eller kalde processen. Det betyder, at kalde processen ikke venter fuldførelse af opgaverne. Derfor registrerer ikke den automatisk, når opgaven stopper.

Hvis du kræver en baggrund opgave til at kommunikere med opgaven opkald for at angive status eller fuldførelse, skal du implementere en funktion til dette. Nogle eksempler er:

- Skriv en status indikator værdi til lager, der er tilgængelige for brugergrænseflade eller opkalds-opgave, og som kan overvåge eller markere denne værdi, når det er påkrævet. Andre data, som opgaven baggrund skal vende tilbage til hvem der kan placeres i den samme lagerplads.
- Oprette et svarkø, brugergrænseflade eller opkalds lytter på. Opgaven baggrund kan sende meddelelser til køen, som angiver status og fuldførelse. Data, som opgaven baggrund skal vende tilbage til hvem der kan placeres i meddelelserne. Hvis du bruger Azure Service Bus, kan du bruge egenskaberne **ReplyTo** og **CorrelationId** for at implementere denne egenskab. Du kan finde yderligere oplysninger finder [korrelation i tjenesten Bus formidlet Messaging](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Få vist en API eller slutpunkt fra den baggrund opgave, brugergrænseflade eller opkalds kan få adgang til for at få oplysninger om status. Data, som opgaven baggrund skal vende tilbage til hvem der skal indgå i svaret.
- Har baggrund opgaven ringe tilbage til Brugergrænsefladen eller opkalds via et API til at angive status på foruddefinerede punkter eller afsluttet. Det kan være via hændelser, der udløses lokalt eller via en Publicer og abonnere ordning. Data, som opgaven baggrund skal returnere kalderen kan medtages i overførslen anmodning eller en begivenhed.

## <a name="hosting-environment"></a>Værtsmiljø

Du kan hoste baggrundsopgaver ved hjælp af en række forskellige Azure-platformstjenester:

- [**Azure Webapps og WebJobs**](#azure-web-apps-and-webjobs). Du kan bruge WebJobs til at udføre brugerdefinerede opgaver, der er baseret på en række forskellige typer scripts eller eksekverbare programmer i forbindelse med en web-app.
- [**Azure Cloud Services internettet og arbejder roller**](#azure-cloud-services-web-and-worker-roles). Du kan skrive programkode i en rolle, som udfører som en baggrund opgave.
- [**Azure virtuelle computere**](#azure-virtual-machines). Hvis du har en Windows-tjeneste, eller du vil bruge Windows Opgavestyring, er det almindelige vært for dine opgaver i baggrunden i en dedikeret virtuel maskine.
- [**Azure batchen**](./batch/batch-technical-overview.md). Det er en platform-tjeneste, der planlægger Beregn-intensivt arbejde at køre på en administreret samling af virtuelle maskiner, og kan automatisk skala beregne ressourcer for at imødekomme dine sager.

I følgende afsnit beskrives hver af disse indstillinger mere detaljeret og medtage overvejelser i forbindelse med for at hjælpe dig med at vælge den ønskede indstilling.

## <a name="azure-web-apps-and-webjobs"></a>Azure Webapps og WebJobs

Du kan bruge Azure WebJobs til at udføre brugerdefinerede opgaver som opgaver i baggrunden i en Azure Web App. WebJobs køre i konteksten for din online som en fortløbende proces. WebJobs også køre i svar på en udløser begivenhed fra Azure Scheduler eller eksterne faktorer, som ændringer af BLOB storage og meddelelseskøer. Job kan startes og stoppes efter behov og lukke problemfrit. Hvis en køre kontinuerligt WebJob mislykkes, skal genstartes den automatisk. Prøv igen og fejloplysninger handlinger kan konfigureres.

Når du konfigurerer en WebJob:

- Hvis du vil jobbet til at reagere på en hændelsesstyret udløser, skal du konfigurere det som **køre kontinuerligt**. Det script eller program er gemt i den mappe med navnet websted/wwwroot/app_data/job/fortløbende.
- Hvis du vil jobbet til at reagere på en tidsplan-driven udløser, skal du konfigurere det som **kører på en tidsplan**. Det script eller program er gemt i den mappe med navnet websted/wwwroot/app_data/job/udløses.
- Hvis du vælger indstillingen **Kør efter behov** , når du konfigurerer et job, udføre den samme kode som indstillingen **Kør på en tidsplan** , når du starter programmet.

Azure WebJobs køre i sandkassetilstand af WebApp. Det betyder, at de kan få adgang til miljøvariabler og dele oplysninger som forbindelsesstrenge, som med WebApp. Jobbet har adgang til det entydige id for den computer, der kører jobbet. Forbindelsesstrengen med navnet **AzureWebJobsStorage** giver adgang til Azure lagerplads køer, BLOB og tabeller for programmet data og adgang til tjenesten Bus messaging og kommunikation. Forbindelsesstrengen med navnet **AzureWebJobsDashboard** giver adgang til loggene job handling.

Azure WebJobs har følgende egenskaber:

- **Sikkerhed**: WebJobs er beskyttet af legitimationsoplysningerne installation af WebApp.
- **Understøttede filtyper**: Du kan definere WebJobs ved hjælp af kommandoscripts (cmd.), batchfiler (.bat), PowerShell-scripts (.ps1), bash shellscripts (.sh), PHP scripts (.php), Python scripts (.py), JavaScript-kode (.js) og eksekverbare programmer (.exe, .jar og mere).
- **Installation**: Du kan installere scripts og eksekverbare filer ved hjælp af portalen Azure ved hjælp af tilføjelsesprogrammet [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) til Visual Studio eller i [Visual Studio 2013 opdatering 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (du kan oprette og installere med denne indstilling), ved hjælp af [Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)eller ved at kopiere dem direkte til følgende steder:
  - Udløste til udførelse af: websted/wwwroot/app_data/job/udløste / {job navn}
  - For fortløbende udførelse af: websted/wwwroot/app_data/job/fortløbende / {job navn}
- **Logføring**: Console.Out fortolkes (markeret) som oplysninger. Console.Error fortolkes som fejl. Du kan få adgang til overvågning og diagnosticering oplysninger ved hjælp af portalen Azure. Du kan hente logfiler direkte fra webstedet. De er gemt på følgende placeringer:
  - Udløste til udførelse af: Vfs/data/job/udløste/jobnavn
  - For fortløbende udførelse af: Vfs/data/job/fortløbende/jobnavn
- **Konfiguration**: Du kan konfigurere WebJobs ved hjælp af portalen, REST-API og PowerShell. Du kan bruge en konfigurationsfil med navnet settings.job i samme rodmappe som job scriptet til at levere konfigurationsoplysninger for en sag. Eksempel:
  - {"stopping_wait_time": 60}
  - {"is_singleton": SAND}

### <a name="considerations"></a>Overvejelser i forbindelse med

- Som standard skalere WebJobs med WebApp. Du kan dog konfigurere job til at køre på enkelt forekomst ved at angive egenskaben **is_singleton** konfiguration til **Sand**. Enkelt forekomst WebJobs er nyttige til opgaver, du ikke vil skalere eller køre som samtidig flere forekomster, som indeksere, dataanalyse og ensartede opgaver.
- For at minimere påvirkningen af sager på ydeevnen af WebApp, kan du overveje at oprette en tom Azure Web App-forekomst i en ny App Service plan til host WebJobs, der kan være lang kører eller ressource intensivt.

### <a name="more-information"></a>Få mere at vide

- [Azure WebJobs anbefalede ressourcer, der](./app-service-web/websites-webjobs-resources.md) viser de mange nyttige ressourcer, overførsler og eksempler til WebJobs.

## <a name="azure-cloud-services-web-and-worker-roles"></a>Azure Cloud Services internettet og arbejder roller

Du kan udføre opgaver i baggrunden i en web rolle eller i en separat arbejder rolle. Når du vælger, om du vil bruge en kollega rolle, kan du overveje skalerbarhed og Elasticitet krav, opgave levetid, slip cadence, sikkerhed, fejltolerance, konflikt, kompleksitet og den logiske arkitektur. Du kan finde yderligere oplysninger finder [Beregne ressource konsolideringen mønster](http://msdn.microsoft.com/library/dn589778.aspx).

Der er flere måder at implementere opgaver i baggrunden i en Cloud Services rolle:

- Oprette en implementering af klassen **RoleEntryPoint** i rollen, og bruge dets metoder til at udføre opgaver i baggrunden. Opgaverne køres i forbindelse med WaIISHost.exe. De kan bruge metoden **GetSetting** i klassen **CloudConfigurationManager** for at indlæse indstillinger for søgekonfiguration. Du kan finde yderligere oplysninger finder [livscyklus (Cloud Services)](#lifecycle-cloud-services).
- Brug Start opgaver til at udføre opgaver i baggrunden, når programmet starter. For at tvinge opgaver for at fortsætte med at køre i baggrunden, skal du angive egenskaben **taskType** til **baggrunden** (Hvis du ikke gør dette, Start ansøgningsprocedure vil standse og vente på opgaven at afslutte). Se [køre Start opgaver i Azure](./cloud-services/cloud-services-startup-tasks.md)kan finde flere oplysninger.
- Bruge WebJobs SDK for at implementere baggrundsopgaver som WebJobs, som er startet som en Start-opgave. Se [Introduktion til Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)kan finde flere oplysninger.
- Bruge en Start opgave til at installere en Windows-tjeneste, som udfører en eller flere opgaver i baggrunden. Du skal angive egenskaben **taskType** på **baggrund** , så tjenesten kører i baggrunden. Se [køre Start opgaver i Azure](./cloud-services/cloud-services-startup-tasks.md)kan finde flere oplysninger.

### <a name="running-background-tasks-in-the-web-role"></a>Køre opgaver i baggrunden i rollen web

Den største fordel ved at køre opgaver i baggrunden i rollen web er gemt i vært omkostninger, fordi der er ingen krav til at udrulle yderligere roller.

### <a name="running-background-tasks-in-a-worker-role"></a>Køre opgaver i baggrunden i en kollega rolle

Køre opgaver i baggrunden i en kollega rolle har flere fordele:

- Gør det muligt at administrere skalering separat for hver type rolle. For eksempel skal du måske flere forekomster af en web rolle til at understøtte den aktuelle belastning, men færre forekomster af rollen arbejder, som udfører opgaver i baggrunden. Ved at skalere baggrund opgave Beregn forekomster adskilt fra rollerne brugergrænseflade, kan du reducere hosting omkostninger, mens du opretholder acceptabel ydeevne.
- Den sender behandling omkostninger for opgaver i baggrunden fra rollen web. Rollen web, der indeholder Brugergrænsefladen kan forblive svarede, og det kan medføre færre forekomster er kræves for at understøtte en given mængde anmodninger fra brugere.
- Gør det muligt at implementere adskillelse af problemer. Hver rolletype kan implementere et bestemt sæt klart defineret og relaterede opgaver. Dette gør designe og vedligeholde koden nemmere, fordi der er mindre indbyrdes sammenhæng mellem kode og funktionalitet mellem hver rolle.
- Det kan hjælpe med at isolere følsomme processer og data. For eksempel behøver web roller, der implementerer Brugergrænsefladen ikke have adgang til data, der administreres og styres af en kollega rolle. Det kan være nyttige i styrke sikkerhed, især hvis du bruger et mønster som [Gatekeeper mønster](http://msdn.microsoft.com/library/dn589793.aspx).  

### <a name="considerations"></a>Overvejelser i forbindelse med

Overvej følgende punkter, når du vælger, hvor og hvordan du installerer opgaver i baggrunden, når du bruger Cloud Services internettet og arbejder roller:

- Vært for opgaver i baggrunden i en eksisterende web rolle kan gemme omkostninger for kører en separat arbejder rolle til disse opgaver. Det er dog kan påvirke ydeevnen og tilgængeligheden af programmet, hvis der er konflikt for kataloger og andre ressourcer. Ved hjælp af en separat arbejder rolle beskytter rollen web fra gennemslagskraft længerevarende eller ressource-intensivt baggrundsopgaver.
- Hvis du er vært for opgaver i baggrunden ved hjælp af klassen **RoleEntryPoint** , kan du nemt flytte det til en anden rolle. Hvis du opretter klassen i en web rolle og senere beslutter, at du skal køre opgaverne i en kollega rolle, kan du flytte **RoleEntryPoint** klasse implementering til rollen arbejder.
- Start opgaver er designet til at udføre et program eller et script. Implementere et baggrundsjob som et eksekverbart program kan være sværere, især hvis også kræver installation af afhængige assemblies. Det kan være nemmere at installere og bruge et script til at definere et baggrundsjob, når du bruger Start opgaver.
- Undtagelser, der kan medføre en baggrund opgave mislykkes har en anden påvirkningen, afhængigt af den måde, de er hostet:
  - Hvis du bruger den **RoleEntryPoint** klasse tilgang, medfører en mislykkedes opgave rollen for at genstarte, så opgaven genstarter automatisk. Det kan påvirke tilgængeligheden af programmet. Sørg for at undgå dette, for at medtage robust undtagelse, der håndterer i klassen **RoleEntryPoint** og alle opgaverne i baggrunden. Bruge kode til at genstarte opgaver, der ikke består af, hvor det er relevant, og Udløs undtagelse for at genstarte rollen, kun, hvis du ikke problemfrit gendanne fra fejlen i din kode.
  - Hvis du bruger Start opgaver, er du ansvarlig for administration af opgave udførelsen og tjekke, hvis det ikke lykkes.
- Administrere og overvåge Start opgaver er sværere end ved hjælp af metoden **RoleEntryPoint** klasse. Men i Azure WebJobs SDK omfatter et dashboard for at gøre det nemmere at administrere WebJobs du starter via Start opgaver.

### <a name="more-information"></a>Få mere at vide

- [Beregne ressource konsolideringen mønster](http://msdn.microsoft.com/library/dn589778.aspx)
- [Introduktion til Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## <a name="azure-virtual-machines"></a>Azure virtuelle maskiner

Baggrundsopgaverne implementeres muligvis på en måde, der forhindrer dem i installeres til Azure Web Apps eller Cloud Services, eller disse indstillinger være ikke en god idé. Typiske eksempler er Windows services og tredjepartsprogrammer og eksekverbare programmer. Et andet eksempel være programmer, der er skrevet til et udførelse af miljø, der er forskellig fra vært for programmet. Det kan for eksempel være et Unix eller Linux-program, du vil udføre fra en Windows- eller .NET program. Du kan vælge mellem en række operativsystemer til en Azure virtuelt og køre din tjeneste eller eksekverbar fil på den virtuelle maskine.

For at hjælpe dig med at vælge, hvornår du skal bruge virtuelle maskiner, se [Azure App Services, Skytjenester og virtuelle maskiner sammenligning](./app-service-web/choose-web-site-cloud-service-vm.md). Du kan finde oplysninger om indstillinger for virtuelle maskiner [virtuelt og skytjeneste størrelser til Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Du kan finde flere oplysninger om de operativsystemer og indbyggede billeder, der er tilgængelige for virtuelle maskiner, [Azure virtuelle maskiner Marketplace](https://azure.microsoft.com/gallery/virtual-machines/).

Hvis du vil starte baggrund opgaven i en separat virtuel maskine, har du en række muligheder:

- Du kan udføre opgaven efter behov direkte fra dit program ved at sende en anmodning til et slutpunkt, som opgaven Fremviser. Dette overfører i de data, der kræver, at opgaven. Dette slutpunkt aktiverer opgaven.
- Du kan konfigurere opgaven skal køres på en tidsplan ved hjælp af en scheduler eller timer, der er tilgængelige i valgte-operativsystemet. For eksempel på Windows-kan du bruge Windows Opgavestyring til at udføre scripts og opgaver. Eller hvis du har SQL Server installeret på den virtuelle maskine, kan du bruge SQL Server Agent for at udføre scripts og opgaver.
- Du kan bruge Azure Scheduler til at starte opgaven, ved at tilføje en meddelelse til en kø, som opgaven lytter på eller ved at sende en anmodning til en API, som opgaven Fremviser.

I afsnittet tidligere [Udløsere](#triggers) kan finde flere oplysninger om, hvordan du kan starte opgaver i baggrunden.  

### <a name="considerations"></a>Overvejelser i forbindelse med

Overvej følgende punkter, når du vælger, om du vil installere opgaver i baggrunden i en Azure virtuelt:

- Vært for opgaver i baggrunden i en separat Azure virtuel maskine giver fleksibilitet og lader nøjagtig kontrol over indledning, udførelse af, planlægning og ressourceallokering. Det vil øge runtime omkostninger, hvis en virtuel maskine skal installeres kun, hvis du vil køre opgaver i baggrunden.
- Der er ingen facilitet til at overvåge opgaverne i portalen Azure og ingen automatiseret genstart egenskab for mislykkede opgaver –, selvom du kan overvåge grundlæggende status for den virtuelle maskine og administrere den ved hjælp af [Azure cmdletter til administration af tjenesten](http://msdn.microsoft.com/library/azure/dn495240.aspx). Der er dog nogen funktioner til at styre processer og tråde i Beregn noder. Typisk kræver ved hjælp af en virtuel maskine ekstra indsats at implementere en funktion, der indsamles data fra instrumentation i opgaven, og fra operativsystemet i virtual machine. En løsning, der kan være relevante er at bruge [System Center Management Pack til Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Du kan overveje at oprette overvågning sonder, der er fremvises HTTP slutpunkter. Koden for disse sonder kan udføre tilstandskontrol, indsamle funktionsdygtige oplysninger og statistik – eller indsamle oplysninger om fejlen og vende tilbage til et administrationsprogram. Du kan finde yderligere oplysninger finder [Sundhed slutpunkt overvågning mønster](http://msdn.microsoft.com/library/dn589789.aspx).

### <a name="more-information"></a>Få mere at vide

- [Virtuelle maskiner](https://azure.microsoft.com/services/virtual-machines/) på Azure
- [Azure virtuelle maskiner ofte stillede spørgsmål](virtual-machines/virtual-machines-linux-classic-faq.md)

## <a name="design-considerations"></a>Designovervejelser

Der findes flere grundlæggende faktorer, du bør overveje, når du designer opgaver i baggrunden. I følgende afsnit beskrives partitionering, konflikter og samarbejde.

## <a name="partitioning"></a>Partitionering

Hvis du beslutter at medtage opgaver i baggrunden i en eksisterende Beregn forekomst (som en WebApp, web rolle, eksisterende arbejder rolle eller virtuelt), skal du overveje, hvordan det påvirker kvalitet attributterne for forekomsten Beregn og baggrund opgaven sig selv. Disse faktorer hjælper dig med at beslutte, om skal colocate opgaver med den eksisterende Beregn forekomst eller Opdel dem i en separat Beregn forekomst:

- **Tilgængelighed**: baggrundsopgaver muligvis ikke har det samme niveau af tilgængelighed som andre dele af programmet, især Brugergrænsefladen og andre dele, der er direkte forbundet i brugerinput. Opgaver i baggrunden kan være mere tolerant af ventetid, forsøgt igen forbindelse fejl, og andre faktorer, der påvirker tilgængelighed, fordi handlingerne kan sættes i kø. Der skal dog være tilstrækkelig kapacitet til at forhindre, at sikkerhedskopieringen af forespørgsler, der kan blokere køer og påvirker programmet som helhed.
- **Skalerbarhed**: baggrundsopgaver har sandsynligvis vil bruge en anden skalerbarhed end Brugergrænsefladen og de interaktive dele af programmet. Skalering Brugergrænsefladen kan være nødvendigt at overholde maksimale efterspørgsel, mens udestående baggrundsopgaver kan udføres under mindre du er optaget af et færre antal Beregn forekomster.
- **Fleksibilitet**: fejl i forbindelse med en forekomst af Beregn, der er bare vært baggrundsopgaver kan ved døden påvirker ikke programmet som helhed Hvis anmodninger om disse opgaver kan være i kø eller udskudt indtil opgaven er tilgængelig igen. Hvis Beregn forekomst og/eller opgaver kan startes i et interval, behov, påvirkes måske ikke brugere af programmet.
- **Sikkerhed**: baggrundsopgaverne kan have forskellige sikkerhedskrav eller begrænsninger end Brugergrænsefladen eller andre dele af programmet. Ved hjælp af en separat Beregn forekomst, kan du angive et miljø med forskellige sikkerhed for opgaverne. Du kan også bruge mønstre som Gatekeeper til at isolere baggrund Beregn forekomster fra Brugergrænsefladen for at maksimere sikkerhed og adskillelse.
- **Ydeevnen**: Du kan vælge typen Beregn forekomst for baggrund opgaver til specifikt match kravene til ydeevne opgaver. Det kan betyde, med en billigere Beregn-indstilling, hvis opgaverne, der ikke kræver de samme behandling funktioner som Brugergrænsefladen eller en forekomst af større, hvis de har brug for yderligere kapacitet og ressourcer.
- **Administration**: baggrundsopgaverne kan have en anden udvikling og installation rytme fra den primære programkode eller Brugergrænsefladen. Implementerer dem i en separat Beregn forekomst kan forenkle opdateringer og versionsstyring.
- **Omkostninger**: tilføje beregne forekomster, der skal udføre baggrund opgaver øges vært omkostninger. Omhyggeligt bør du overveje at tage mellem ekstra kapacitet og disse ekstra omkostninger.

Se [Fyldtegn valg](http://msdn.microsoft.com/library/dn568104.aspx) og [Konkurrere forbrugere mønster](http://msdn.microsoft.com/library/dn568101.aspx)kan finde flere oplysninger.

## <a name="conflicts"></a>Konflikter

Hvis du har flere forekomster af et baggrundsjob, er det muligt, at de er konkurrenter for at få adgang til ressourcer og tjenester, som databaser og lager. Denne samtidige adgang kan medføre ressourcekonflikter, som kan der opstå konflikter i tilgængelighed af tjenesterne og i integriteten af dataene i lager. Du kan løse ressourcekonflikter ved hjælp af en pessimistisk låsning fremgangsmåde. Dette forhindrer, at konkurrere forekomster af en opgave fra samtidig at få adgang til en tjeneste eller ødelægge data.

En anden metode til at løse konflikter er at definere opgaver i baggrunden som en separat, så der er kun nogensinde én forekomst, der kører. Dette fjerner dog pålideligheden og ydeevnen fordele, der kan overføre en konfiguration med flere forekomst. Dette er især SAND, hvis Brugergrænsefladen kan levere tilstrækkelige arbejde for at holde mere end én baggrund opgave optaget.

Det er meget vigtigt at sikre, at opgaven baggrund kan automatisk genstarter og at den har tilstrækkelig kapacitet til at imødegå maksimale efterspørgsel. Du kan opnå ved at tildele en forekomst af Beregn med tilstrækkelige ressourcer ved at implementere en sætter i kø ordning, der kan gemme anmodninger om senere kørsel når demand mindskes eller ved at bruge en kombination af disse metoder.

## <a name="coordination"></a>Køre koordineret

Opgaverne i baggrunden kan være kompleks og kræver muligvis flere individuelle opgaver til at udføre til at producere resultatet eller opfylder kravene. Det er fælles for disse scenarier for at inddele opgaven i mindre diskret trin eller underopgaver, der kan udføres af flere forbrugerne. Forbindelse med resultatmønstre job kan være mere effektiv og mere fleksibel, fordi enkelte trin kan være der kan genbruges i flere sager. Det er også nemt at tilføje, fjerne eller ændre rækkefølgen af trinnene.

Koordinere flere opgaver og trin kan være en udfordring, men der er tre almindelige mønstre, som du kan bruge til at hjælpe din implementering af en løsning:

- **Decomposing en opgave til flere der kan genbruges trin**. Et program muligvis bedt om at udføre en række opgaver af variabel kompleksitet på de oplysninger, som det behandler. En enkle, men ikke-fleksibel metode til at implementere dette program kan være til at udføre denne behandling som et monolitisk modul. Denne metode er dog sandsynligvis at reducere mulighederne for opstod koden, optimering af det eller genbruge den, hvis dele af den samme behandling der kræves et andet sted i programmet. Få mere at vide under [Pipes og filtre mønster](http://msdn.microsoft.com/library/dn568100.aspx).
- **Administrere udførelse af trinnene for en opgave**. Et program kan udføre opgaver, der udgør en lang række trin (hvoraf kan kalde remote tjenester eller få adgang til eksterne ressourcer). De enkelte trin kan være uafhængigt af hinanden, men de er orchestrated ved programmet logik, der implementerer opgaven. Du kan finde yderligere oplysninger finder [Scheduler Agent overordnede mønster](http://msdn.microsoft.com/library/dn589780.aspx).
- **Administrere gendannelse opgave vejledning, der mislykkes**. Et program kan være nødvendigt at fortryde det arbejde, der er udført af en række trin (som tilsammen definerer handlingen til sidst ensartet), hvis ét eller flere af trinnene mislykkes. Du kan finde yderligere oplysninger finder [Kompensation transaktion mønster](http://msdn.microsoft.com/library/dn589804.aspx).

## <a name="lifecycle-cloud-services"></a>Livscyklus (Cloud Services)

 Hvis du beslutter at implementere baggrundsjob for Cloud Services-programmer, der bruger internettet og arbejder roller ved hjælp af klassen **RoleEntryPoint** , er det vigtigt at forstå klassen livscyklus for at bruge den korrekt.

Internettet og arbejder roller gå gennem en række forskellige faser, som de start, køre, og stop. Klassen **RoleEntryPoint** Fremviser en række hændelser, der angiver, når disse tre faser virkelighed. Du bruger disse initialiseret, kører, og stop din brugerdefinerede baggrundsopgaverne. Fuldført cyklussen er:

- Azure indlæser samlingen rolle og søger efter en klasse, der stammer fra **RoleEntryPoint**.
- Hvis der bliver fundet klassen, kalder **RoleEntryPoint.OnStart()**. Du kan tilsidesætte denne metode til at initialiseret dine opgaver i baggrunden.
- Når metoden **OnStart** er fuldført, præsentere Azure opkald **Application_Start()** i programmets globale fil, hvis dette er (for eksempel Global.asax i en web rolle, der kører ASP.NET).
- Azure opkald **RoleEntryPoint.Run()** på en ny forgrunden tråd, som udfører parallelt med **OnStart()**. Du kan tilsidesætte denne metode for at starte dine opgaver i baggrunden.
- Når metoden Run afsluttes, ringer Azure først **Application_End()** i programmets globale fil, hvis dette er til stede, og derefter kalder **RoleEntryPoint.OnStop()**. Du kan tilsidesætte metoden **OnStop** for at stoppe dine opgaver i baggrunden, rydde op i ressourcer, sælge objekter, og Luk forbindelser, der muligvis har brugt på opgaver.
- Azure rolle host arbejdsprocessen er stoppet. På dette tidspunkt rollen skal genbruges og genstarter.

Få mere at vide og et eksempel på ved hjælp af metoderne i klassen **RoleEntryPoint** under [Beregne ressource konsolideringen mønster](http://msdn.microsoft.com/library/dn589778.aspx).

## <a name="considerations"></a>Overvejelser i forbindelse med

Overvej følgende punkter, når du har planer om hvordan du udfører opgaver i baggrunden i en web eller arbejder rolle:

- Standard **køre** metode implementeringen i klassen **RoleEntryPoint** indeholder et opkald til **Thread.Sleep(Timeout.Infinite)** , som bevarer rollen Live på ubestemt tid. Hvis du tilsidesætter metoden **køre** (som er typisk nødvendigt at udføre opgaver i baggrunden), skal du ikke tillade din kode for at afslutte fra metoden, medmindre du vil genbruge den rolle forekomst.
- En typisk implementering af metoden **køre** indeholder kode for at starte hver af opgaverne i baggrunden og en løkke konstruktion, der kontrollerer regelmæssigt tilstanden for alle opgaverne i baggrunden. Det kan genstarte som mislykkes eller overvåge for annulleringen tokens, som angiver, at job har fuldført.
- Hvis en baggrund opgave medfører en ikke-afviklet undtagelse, skal denne opgave genbruges samtidig med andre opgaver i baggrunden i rollen til at fortsætte. Hvis undtagelsen skyldes beskadigelse af objekter uden for opgaven, som delt storage, undtagelsen skal håndteres af klasse **RoleEntryPoint** , alle opgaver skal annulleres, og metoden **køre** skal have lov til at afslutte. Azure genstart derefter rollen.
- Du kan bruge metoden **OnStop** til at standse eller Slet baggrundsopgaver og rydde op i ressourcer. Dette kan omfatte stoppe længerevarende eller forbindelse med resultatmønstre opgaver. Det er vigtigt at overveje, hvordan du kan gøre for at undgå datauoverensstemmelser. Hvis en forekomst af rolle stopper for en eller anden grund end en brugerinitieret lukning, skal den kode, der kører i metoden **OnStop** fuldføres inden for fem minutter, før den gennemtvinge er afsluttet. Sikre, at din kode kan udføres i periode eller kan acceptere ikke kører til afslutning.  
- Azure belastning starter dirigerer trafik til den rolle-forekomst, når metoden **RoleEntryPoint.OnStart** returnerer værdi **sandt**. Derfor kan du overveje at placere alle initialisering koder i metoden **OnStart** så rolle forekomster, der ikke korrekt initialiseret ikke modtager al trafik.
- Du kan bruge Start opgaver ud over metoder til klassen **RoleEntryPoint** . Du skal bruge Start opgaver initialiseret nogen indstillinger, du vil ændre i Azure justering af belastning, da disse opgaver skal udføres, før rollen modtager en hvilken som helst anmodninger. Se [køre Start opgaver i Azure](./cloud-services/cloud-services-startup-tasks.md)kan finde flere oplysninger.
- Hvis der er en fejl i en Start-opgave, kan det gennemtvinge rollen kontinuerligt genstarte. Dette kan forhindre dig i at udføre en virtuelle IP-adresse (VIP) adresse Udskift tilbage til en tidligere faseinddelt version, da Udskift kræver udelt adgang til rollen. Dette kan ikke hentes, mens rollen genstarter. Sådan løses problemet:
    -  Føj følgende kode til starten af metoderne **OnStart** og **køre** i din rolle:

    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
        if (Boolean.Parse(freeze))
        {
            Thread.Sleep(System.Threading.Timeout.Infinite);
        }
    }
    ```

   - Tilføje definitionen af indstillingen **fryse** som en boolesk værdi til ServiceDefinition.csdef og ServiceConfiguration. *.cscfg filer til rollen og angive den til* *Falsk* *. Hvis rolle er i en gentagen genstart tilstand, kan du ændre indstillingen til * *Sand** du fryser udførelse af rolle og tillade den byttes med en tidligere version.

## <a name="resiliency-considerations"></a>Overvejelser om fleksibilitet

Baggrundsopgaverne skal være tolerant for at levere pålidelig tjenester til programmet. Når du planlægger og designe opgaver i baggrunden, skal du overveje følgende punkter:

- Baggrundsopgaverne skal kunne håndtere problemfrit rolle eller tjeneste genstarter uden ødelægge data eller introducerer uoverensstemmelse i programmet. Overvej at bruge _Kontroller, der peger_ ved at gemme tilstanden for job i fast lagerplads, eller som meddelelser i en kø, hvis det er relevant for længerevarende eller forbindelse med resultatmønstre opgaver. Du kan for eksempel fastholdes tilstandsoplysninger i en meddelelse i en kø og trinvist opdatere oplysningerne tilstand med fremskridt for en opgave, så opgaven kan behandles fra det sidste kendte kontrolpunkt som god – i stedet for at genstarte fra begyndelsen. Når du bruger Azure Service Bus køer, kan du bruge meddelelse sessioner til at aktivere det samme scenario. Sessioner giver dig mulighed at gemme og hente tilstanden programmet behandling ved hjælp af metoderne [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) og [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) . Du kan finde flere oplysninger om design af pålidelig forbindelse med resultatmønstre processer og arbejdsprocesser, [Scheduler Agent overordnede mønster](http://msdn.microsoft.com/library/dn589780.aspx).
- Designe din tilsidesættelse af den **køre** metode til at overvåge for mislykkedes eller opdage opgaver, og genstart dem, når du bruger web eller arbejder roller til at hoste flere opgaver i baggrunden. Hvis dette ikke er praktiske, og du bruger en kollega rolle, du gennemtvinge rollen arbejder for at genstarte ved at afslutte fra metoden **køre** .
- Når du bruger køer til at kommunikere med opgaver i baggrunden, køerne kan fungere som et bufferen til at gemme anmodninger, der sendes til opgaverne, når programmet findes under højere end normalt Indlæs. Dette giver mulighed for opgaver for at holde trit med Brugergrænsefladen i mindre belastet perioder. Det betyder også, at genbrug rollen ikke blokerer Brugergrænsefladen. Se [kø-baserede Indlæs udjævning mønster](http://msdn.microsoft.com/library/dn589783.aspx)kan finde flere oplysninger. Hvis nogle opgaver er vigtigere end andre, kan du overveje at implementere [Prioritet kø mønster](http://msdn.microsoft.com/library/dn589794.aspx) for at sikre, at disse opgaver køre før mindre vigtige dem.
- Opgaver i baggrunden, som er startet af meddelelser eller proces meddelelser skal være designet til at håndtere uoverensstemmelser, som meddelelser ankommer defekt, meddelelser, der kan medføre flere gange (ofte kaldet _uautoriserede meddelelser_) fejl og meddelelser, der leveres mere end én gang. Overvej følgende:
  - Meddelelser, der skal behandles i en bestemt rækkefølge, som dem, ændrer data, der er baseret på den eksisterende værdi for data (for eksempel, tilføje en værdi til en eksisterende værdi), skal du muligvis ikke ender i den oprindelige rækkefølge, hvori de blev sendt. Alternativt, kan de håndteret af forskellige forekomster af en baggrund opgave i en anden rækkefølge på grund af varierende belastning på de enkelte forekomster. Meddelelser, der skal behandles i en bestemt rækkefølge skal indeholde en række tal, nøgle eller nogle andre indikator, der baggrundsopgaverne kan bruge til at sikre, at de er behandlet i den rigtige rækkefølge. Hvis du bruger Azure Service Bus, kan du bruge meddelelse sessioner for at garantere rækkefølgen af levering. Det er dog normalt mere effektivt, hvor det er muligt at designe processen, så Meddelelsernes rækkefølge ikke er vigtige.
  - Typisk kan en baggrund opgave vise meddelelser i køen, som midlertidigt skjuler dem fra andre forbrugere af meddelelse. Den sletter derefter meddelelserne, når de er blevet behandlet korrekt. Hvis en baggrund mislykkes, når behandling af en meddelelse, vises denne meddelelse igen i køen efter oversigten timeout udløber. Det vil blive behandlet af en anden forekomst af opgaven eller kun inden for den næste behandling livscyklussen for denne forekomst. Hvis meddelelsen konsekvent medfører en fejl i forbrugeren, vil det blokere opgaven, køen og til sidst selve programmet, når køen er fuld. Det er vigtigt at finde og fjerne uautoriserede meddelelser fra køen. Hvis du bruger Azure Service Bus, kan meddelelser, der medfører en fejl, flyttes automatisk eller manuelt til en tilknyttet inaktive bogstav kø.
  - Køer er garanti på _mindst én gang_ medie, men de kan levere den samme meddelelse mere end én gang. Desuden, hvis en baggrund mislykkes, efter behandling af en meddelelse, men før du sletter den fra køen skal bliver meddelelsen tilgængelig til at behandle igen. Baggrundsopgaverne skal være idempotent, hvilket betyder, at behandle den samme meddelelse mere end én gang ikke medfører en fejl eller uoverensstemmelse i programmets data. Nogle handlinger er naturligt idempotent, såsom angive en lagrede værdi til en bestemt ny værdi. Dog medfører handlinger som føjer en værdi til en eksisterende lagrede værdi uden at kontrollere, at den lagrede værdi er stadig den samme som når meddelelsen oprindeligt blev sendt uoverensstemmelser. Azure Service Bus køer kan konfigureres til automatisk for at fjerne dublerede meddelelser.
  - Nogle SMS systemer, som Azure lagerplads køer og Azure Service Bus køer understøtter en de-queue Tæl egenskab, der angiver antallet gange, der er blevet læst en meddelelse fra køen. Det kan være nyttige i håndtering af gentagne og en meddelelser. Se [Asynkron Messaging grundlæggende](http://msdn.microsoft.com/library/dn589781.aspx) og [Redundanskontrol mønstre](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/)kan finde flere oplysninger.

## <a name="scaling-and-performance-considerations"></a>Overvejelser vedrørende skalering og ydeevne

Opgaver i baggrunden, skal give tilstrækkelige ydeevne for at sikre, at de ikke blokere programmet eller skabe uoverensstemmelser på grund af forsinket handling, når systemet er belastning. Typisk forbedre ydeevnen ved skalering Beregn forekomster, der hoster opgaverne i baggrunden. Når du planlægger og designe opgaver i baggrunden, skal du overveje følgende punkter omkring skalerbarhed og ydeevne:

- Azure understøtter Autoskalering (både skalering af og skalering tilbage i) baseret på aktuelle behov og belastning – eller på en foruddefineret tidsplan for Web Apps, Cloud Services web og arbejder roller og virtuelle maskiner hostet installationer. Brug denne funktion til at sikre, at programmet som helhed har tilstrækkelig ydeevne funktioner minimerer runtime omkostningerne.
- Hvor baggrundsopgaverne har en anden ydeevne egenskab fra de øvrige dele af et Cloud Services-program (for eksempel brugergrænseflade eller komponenter som data access lag), vært baggrundsopgaverne sammen i en separat arbejder rolle giver mulighed for Brugergrænsefladen og baggrund opgave roller skalere uafhængigt til at administrere afkrydsningsfeltet Indlæs. Hvis flere opgaver i baggrunden har signifikant forskellige ydeevne funktioner fra hinanden, skal du overveje at opdele dem i separate arbejder roller og skalering hver rolletype uafhængigt af hinanden. Bemærk, at dette kan øge runtime omkostninger sammenlignet med at alle opgaverne i færre roller.
- Blot skalering rollerne, der muligvis ikke er tilstrækkelige til at undgå tab af ydeevne belastning. Du muligvis også skalere lagerplads køer og andre ressourcer for at forhindre et enkelt punkt af samlet behandling kæde fra bliver en flaskehals. Overvej også at andre begrænsninger, som det maksimale antal gennemløb for lagerplads og andre tjenester, der er afhængige programmet og opgaverne i baggrunden.
- Baggrundsopgaverne skal være udviklet til skalering. For eksempel skal de kunne registrere dynamisk antallet af lagerplads køer i brug for at lytte på eller sende meddelelser til den relevante kø.
- Som standard WebJobs Skaler med deres tilknyttede Azure Web Apps-forekomst. Men hvis du vil en WebJob til at køre som en enkelt forekomst, kan du oprette en Settings.job-fil, der indeholder dataene, JSON **{"is_singleton": SAND}**. Dette tvinger Azure kun køre én forekomst af WebJob, selvom der er flere forekomster af tilknyttede WebApp. Det kan være en praktisk metode til planlagte opgaver, der skal køre som en enkelt forekomst.

## <a name="related-patterns"></a>Relaterede mønstre

- [Asynkron SMS grundlæggende](http://msdn.microsoft.com/library/dn589781.aspx)
- [Autoskalering vejledning](http://msdn.microsoft.com/library/dn589774.aspx)
- [Kompensation transaktion mønster](http://msdn.microsoft.com/library/dn589804.aspx)
- [Konkurrerende forbrugere mønster](http://msdn.microsoft.com/library/dn568101.aspx)
- [Beregne partitionering vejledning](http://msdn.microsoft.com/library/dn589773.aspx)
- [Beregne ressource konsolideringen mønster](http://msdn.microsoft.com/library/dn589778.aspx)
- [Gatekeeper mønster](http://msdn.microsoft.com/library/dn589793.aspx)
- [Fyldtegn valg mønster](http://msdn.microsoft.com/library/dn568104.aspx)
- [Pipes og filtre mønster](http://msdn.microsoft.com/library/dn568100.aspx)
- [Prioritet kø mønster](http://msdn.microsoft.com/library/dn589794.aspx)
- [Kø-baserede Indlæs udjævning mønster](http://msdn.microsoft.com/library/dn589783.aspx)
- [Scheduler Agent overordnede mønster](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>Få mere at vide

- [Skalering Azure-programmer med arbejder roller](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [Udføre opgaver i baggrunden](http://msdn.microsoft.com/library/ff803365.aspx)
- [Azure rolle Start livscyklus](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (blogindlæg)
- [Azure Cloud Services rolle livscyklus](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (video)
- [Introduktion til Azure WebJobs SDK](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Azure køer og Service Bus køer - sammenlignet og i farvekategorier](./service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Sådan aktiveres diagnosticering i en skybaseret tjeneste](./cloud-services/cloud-services-dotnet-diagnostics.md)
