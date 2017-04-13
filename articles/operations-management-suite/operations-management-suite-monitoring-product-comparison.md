<properties 
   pageTitle="Microsoft overvågning sammenligning af produkter | Microsoft Azure"
   description="Microsoft Operations Management pakke (OMS) er Microsofts skyen baseret IT management-løsning, der hjælper dig med at administrere og beskytte dine lokale og infrastruktur i skyen.  I denne artikel identificerer de forskellige tjenester, der er inkluderet i OMS og indeholder links til deres detaljerede indhold."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="microsoft-monitoring-product-comparison"></a>Sammenligning af Microsoft overvågning produkter

I denne artikel indeholder en sammenligning mellem System Center Operations Manager (SCOM) og Log Analytics i handlinger Management pakke (OMS) med hensyn til deres arkitektur, logik om hvordan de overvåge ressourcer, og hvordan de udføre analyse af de data, de indsamler.  Dette er at give dig en grundlæggende forståelse af deres forskelle og relative styrker.  

## <a name="basic-architecture"></a>Grundlæggende arkitektur
### <a name="system-center-operations-manager"></a>System Center Operations Manager

Alle SCOM komponenter er installeret i dit datacenter.  [Supportmedarbejdere er installeret](http://technet.microsoft.com/library/hh551142.aspx) på Windows og Linux-computere, der administreres af SCOM.  Supportmedarbejdere forbindelse til [Styring af servere](https://technet.microsoft.com/library/hh301922.aspx) kommunikere med SCOM database og data warehouse.  Supportmedarbejdere, der er afhængige af godkendelse af domæner til at oprette forbindelse til styring af-servere.  Dem uden for et domæne, der er tillid til kan udføre certifikatgodkendelse eller oprette forbindelse til en [Gateway-serveren](https://technet.microsoft.com/library/hh212823.aspx).

SCOM kræver to SQL-databaser, én til funktionsdygtige data og en anden datalager til at understøtte rapportering og analyse.  En [Reporting Server](https://technet.microsoft.com/library/hh298611.aspx) kører SQL Reporting Services til at rapportere om data fra et datalager. 

SCOM kan overvåge skyen ressourcer ved hjælp af management packs for produkter som [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708)og [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Disse management packs Brug en eller flere lokale supportmedarbejdere som proxyer for registrering skyen ressourcer og arbejdsprocesser, der kører for at måle deres ydeevne og tilgængelighed.  Proxy supportmedarbejdere også bruges til at [overvåge netværksenheder](https://technet.microsoft.com/library/hh212935.aspx) og andre eksterne ressourcer.

Konsollen handlinger er en Windows-program, der opretter forbindelse til en af management-serverne og gør det muligt for administratoren for at få vist og analysere indsamlede data og konfigurere SCOM-miljø.  En webbaseret konsol kan knyttes til en hvilken som helst IIS-server og giver dataanalyse via en browser.

![SCOM arkitektur](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics

De fleste OMS komponenter er i Azure skyen, så du kan installere og administrere med minimale omkostninger og administrative arbejde.  Alle de data, der indsamles via Log Analytics er gemt i OMS-lager.

Log Analytics kan Indsaml data fra en af tre måder:

- Fysiske og virtuelle maskiner, der kører Windows og [Microsoft overvågning Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) eller Linux og [Handlinger Management pakke Agent for Linux](https://technet.microsoft.com/library/mt622052.aspx).  Disse computere kan være lokalt eller virtuelle maskiner i Azure eller en anden skyen.
- En Azure-lager konto med [Azure diagnosticering](../cloud-services/cloud-services-dotnet-diagnostics.md) data, der indsamles via Azure arbejder rolle, web rolle eller virtuelt.
- [Forbindelse til en SCOM management gruppe](https://technet.microsoft.com/library/mt484104.aspx).  I denne konfiguration kommunikere supportmedarbejdere med SCOM management servere, der leverer data til databasen SCOM hvor leveres det derefter til OMS datalager.
Administratorer analysere indsamlede data og konfigurere Log Analytics med portalen OMS som hostes i Azure og kan åbnes fra en hvilken som helst browser.  Mobilapps til at få adgang til disse data er tilgængelige for de almindelige platforme.

![Log Analytics arkitektur](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integrere SCOM og Log Analytics

Når SCOM bruges som datakilde til Log Analytics kan du udnytte funktionerne i begge produkter i en hybrid overvågning miljø.  Du kan konfigurere eksisterende SCOM supportmedarbejdere gennem konsollen handlinger der skal administreres af OMS, ud over fortsætter med at køre management packs fra SCOM.  
Data fra en forbundet SCOM management gruppe er leveret til Log analyser ved hjælp af en af fire metoder:

- Begivenheder og ydelsesdata indsamles af agenten og leveret til SCOM.  Administration af servere i SCOM levér derefter dataene til Log analyser.
- Nogle begivenheder som IIS-logfiler og sikkerhed begivenheder stadig leveres direkte til Log Analytics fra en agent.
- Nogle løsninger, der skal levere yderligere software til agenten eller kræver, at software skal installeres, hvis du vil indsamle flere oplysninger.  Disse data sendes typisk direkte til Log analyser.
- Nogle løsninger indsamle data direkte fra SCOM styring af servere, der ikke stammer fra en agent.  For eksempel indsamler [beskeder om Management-løsning](https://technet.microsoft.com/library/mt484092.aspx) beskeder fra SCOM, når de er blevet oprettet.

## <a name="monitoring-logic"></a>Overvågning logik
SCOM og Log Analytics arbejde med lignende data, der indsamles fra supportmedarbejdere, men har fundamentale forskelle i hvordan de definere og implementere deres logik for indsamling af data og hvordan de analysere de data, de indsamler.

### <a name="operations-manager"></a>Operations Manager
Overvågning logik for SCOM er implementeret i [management packs](https://technet.microsoft.com/library/hh457558.aspx) , der indeholder logik for opdager komponenter til at overvåge, måle disse komponenter, og til indsamling af data til at analysere sundhed.  Overvågning af data kan være så enkelt som indsamling af en begivenhed eller ydeevne tæller, eller den kan bruge kompleks logik, der er implementeret i et script.  Management packs, der omfatter fuldført overvågning er tilgængelige for en række forskellige [Microsoft og tredjepartsprogrammer](http://go.microsoft.com/fwlink/?LinkId=82105) ud over hardware- og netværksenheder.  Du kan [redigere dine egne management packs](http://aka.ms/mpauthor) til brugerdefinerede programmer.

Management packs indeholder flere arbejdsprocesser, hver udfører nogle overvågning funktionen distinct som indsamle en ydeevne tæller, kontrollere tilstanden for en tjeneste eller kører et script.  Hver arbejdsprocessen kører uafhængigt af hinanden og definerer sin egen resultater som hvilken database det skriver til, og om det genererer en besked. 

Du kan tilsidesætte oplysninger om arbejdsproces som den hyppighed, de kører, i grænseværdi, hvor de finder en fejl, og alvorlighed af påmindelsen for de genererer.  Du kan også angive yderligere funktioner ved at tilføje dine egne arbejdsprocesser.

![Tilsidesætter](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Management packs er installeret i Operations Manager-database og automatisk distribueres til supportmedarbejdere af management-servere.  Hver agent bliver automatisk kan hente management packs og Indlæs arbejdsprocesser, der er relevante for de programmer, de har installeret.  Data, der indsamles af agenten leveres tilbage til indholdsstyringsserver indsættes i SCOM database og data warehouse.  Konsollen handlinger kan du få vist og analysere dataene gennem brugerdefinerede visninger, dashboards og rapporter, der er inkluderet i management pack.

Fordelingen af management packs illustreres i følgende diagram.

![Management pack flow](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Begivenhed og ydeevnen af websteder
Log Analytics indsamler begivenheder og tællere i ydeevne fra en agent, der bruger kilder som Windows-hændelsesloggen, IIS-logfiler og Syslog.  Du kan angive kriterier som data indsamles via Log Analytics-portalen og derefter oprette Log forespørgsler for at analysere de indsamlede data.  Et sæt standard kriterier er defineret, når du opretter dit OMS arbejdsområde, og du kan angive yderligere data for bestemte programmer. 

![Definere hændelseslogfiler i Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Mens SCOM har mange detaljerede arbejdsprocesser, der typisk definerer specifikke kriterier for data og den handling, der skal udføres i svar, har Log Analytics mere generelle kriterier for indsamling af data.  Log forespørgsler og løsninger, der giver mere målrettede kriterier til at analysere og Rådet på bestemte data i skyen, når den er blevet indsamlet.

#### <a name="solutions"></a>Løsninger
Løsninger, der giver yderligere logik for indsamling af data og analyse.  Du kan vælge solutions for at føje til dit OMS abonnement fra løsningsgalleriet.

![Løsningsgalleriet](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Løsninger køre primært i skyen give analyse af begivenheder og ydeevne tællere, der indsamles i OMS-lager.  De kan også angive yderligere data, der indsamles, der kan analysere med Log forespørgsler eller via yderligere brugergrænseflade, der leveres af løsningen i dashboardet OMS. 

For eksempel [ændringsregistrering løsning](https://technet.microsoft.com/library/mt484099.aspx) registrerer ændringer i konfigurationen på agent systemer og skriver begivenheder til OMS-lager, der kan analysere med flere grafiske visninger, som opsummerer registreret ændringer.  Du kan fokusere fra visningen opsummerede i log forespørgsler, der viser de detaljerede data, der indsamles via løsningen.


Du kan vælge, hvilke løsninger, du føjer til dit abonnement, men du ikke aktuelt har mulighed for at oprette dine egne løsninger.  Du kan vælge de hændelser og ydeevne tællere til at samle og oprette brugerdefinerede visninger, der er baseret på dine egne log forespørgsler.

Overvågning logik for Log analyser er opsummeret i følgende diagram.

![Log løsningen Analytics flow](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Overvåge systemtilstand
### <a name="operations-manager"></a>Operations Manager
SCOM kan modellere de forskellige komponenter i et program, og Angiv en realtid tilstand for hver.  Her kan du ikke kun visning, der er fundet fejl og ydeevne over tid, men også til at validere faktisk tilstanden for et program eller system og de enkelte komponenter til enhver tid.  Da den forstår de tidsperioder, et program er tilgængelig, understøtter sundhed program i SCOM også Service niveau aftaler (SLA), analysere og rapportere om tilgængeligheden af et program over tid.

For eksempel viser visningen nedenfor realtid tilstanden for SQL-databaseprogrammer overvåges af SCOM.  Tilstanden for hver enkelt database til en af databaseprogrammer vises på nederst halvdel af visningen.

![Stat visning](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

Sundhed Explorer for et af databaseprogrammer er vist nedenfor med de skærme, der bruges til at bestemme den overordnede sundhed.  Disse skærme er defineret i SQL management pack og kører i forhold til alle SQL databaseprogrammer, der er registreret af SCOM.

![Sundhed explorer](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Komponenter på flere systemer kan kombineres for at måle tilstanden for et distribueret program.  Det kan være nyttig for line of business-applikationer, der indeholder flere fordelt komponenter.  Du kan oprette en model, der måler tilstanden for hver komponent, opdateringspakke til tilgængelighed for programmet.

Active Directory er et eksempel på en management pack, der indeholder en model for at analysere dens fordelt komponenter.  Eksempel diagrammet nedenfor viser tilstanden for det overordnede miljø og relationen mellem områder, domæner og domænecontrollere.  Hver af disse komponenter omfatter underkomponenter og flere skærme ligner SQL eksemplet ovenfor.

![SCOM diagramvisning](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS ikke omfatter et almindelige program til model programmer eller måle deres realtid tilstand.  Individuelle løsninger kan vurdere den overordnede sundhed for bestemt tjenester, der er baseret på indsamlede data og de kan installere brugerdefineret logik på agent til analyse af realtid.  Da løsninger, der kører i skyen med adgang til OMS lager, kan de ofte giver bedre analyse end udføres typisk af management packs. 

For eksempel [AD vurdering og SQL vurdering løsninger](https://technet.microsoft.com/library/mt484102.aspx) analysere indsamlede data og giver en klassificering af forskellige aspekter af miljøet.  Den indeholder anbefalinger til forbedringer, der kan foretages til at forbedre tilgængelighed og ydeevnen af miljøet.

![AD vurdering løsning](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Dataanalyse
SCOM og Log Analytics giver forskellige funktioner for at analysere indsamlede data.  SCOM har visninger og Dashboards i konsollen handlinger til at analysere seneste data i en række forskellige formater og rapporter til at præsentere data fra et datalager i tabelform.  Log Analytics indeholder en fuldstændige log forespørgselssprog og grænseflade til analyse af data i OMS-lager.  Når SCOM bruges som datakilde til Log Analytics, omfatter lageret data, der indsamles via SCOM, så Log Analytics-værktøjerne kan bruges til at analysere data fra begge systemer.

### <a name="operations-manager"></a>Operations Manager

#### <a name="views"></a>Visninger
Visninger i konsollen handlinger kan du få vist forskellige datatyper, der indsamles af SCOM i forskellige formater, typisk tabelformat til begivenheder, beskeder og tilstand data og kurvediagrammer for ydelsesdata.  Visninger udføre minimale analyse eller konsolidering af dataene men tillader, at du til at filtrere efter bestemt kriterier. 

![Visninger](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Management packs typisk give flere visninger, der understøtter programmet eller system, overvåger.  Dette kan omfatte tilstand visninger for de forskellige objekter, management pack opdager, besked om der er fundet problemer, og ydeevnen visninger for tællere.

Visninger er særligt egnet til analyse af den aktuelle tilstand for miljøet herunder Åbn beskeder og tilstanden for overvåget systemer og objekter.  Du kan analysere ned til detaljerede begivenhed eller ydelsesdata understøttende en bestemt besked for at diagnosticere den egentlige årsag. På samme måde, kan du se den ydeevne og tilstand for forskellige dele af et program tilladelse til at vurdere den aktuelle tilstand.

#### <a name="dashboards"></a>Dashboards
Dashboards i konsollen handlinger fungerer primært med de samme data, som visninger men højere grad kan tilpasses og kan indeholde mere omfattende visualiseringer.  Et sæt af standard dashboards er tilgængelige, kan du nemt tilpasse til dine egne formål.  Du kan også bruge en PowerShell-widget der kan vise data, der returneres fra en PowerShell-forespørgsel.

![Dashboard](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Udviklere har mulighed for at tilføje brugerdefinerede komponenter til de omfatter i deres management packs dashboards.  Disse kan yderst specialiserede til et bestemt program som dashboardet i SQL management pack vist nedenfor.  Dette dashboard kan også bruges som en skabelon til brugerdefineret versioner.

![SQL-Dashboard](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Rapporter
Rapporter i SCOM analyserer data fra et datalager i tabelform.  De kan udskrives og planlagt for automatiseret levering i forskellige filformater, herunder PDF-fil, csv- og Word.  Rapporter arbejde med data fra et datalager, så de er især egnet til analyse af langsigtede tendenser.

Management packs typisk give brugerdefinerede rapporter til et bestemt program.  Du kan også vælge fra et bibliotek med generisk rapporter, som du kan tilpasse til dine egne programmer eller for at udføre ad hoc-analyser.

Følgende er en eksempelrapport i ydeevne, der viser data, der indsamles via Active Directory Management Pack.

![Rapport](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics har et [forespørgselssprog](https://technet.microsoft.com/library/mt484120.aspx) , som du kan bruge til at analysere på tværs af data fra flere programmer uden at skulle oprette en brugerdefineret visning eller rapport.  Da OMS er implementeret i skyen, ydeevnen af forespørgsler og dataanalyse er ikke underlagt Hardwarebegrænsninger og kan hurtig analyse af forespørgsler, herunder millioner af poster. 

Forespørgsler i Log Analytics er også grundlaget for andre funktioner.  Du kan gemme en forespørgsel, eksportere resultaterne til Excel, eller har det automatisk at køre med jævne mellemrum og oprette en besked, hvis resultaterne opfylder bestemte kriterier.  

![Log forespørgsel flow](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

Nedenfor vises et eksempel på en Log Analytics-forespørgsel.  I dette eksempel alle begivenheder med "startet" i navnet returneres og grupperet efter begivenhed-id.  Brugeren leverer blot forespørgslen, og Log Analytics genererer dynamisk brugergrænsefladen for at udføre analysen.  Valg af et element på listen vil returnere de detaljerede begivenhed data.

![Log forespørgsel](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Ud over at give ad hoc-analyser, kan forespørgsler i Log Analytics gemt til fremtidig brug og også føjet til dit [OMS dashboard](http://technet.microsoft.com/library/mt484090.aspx) , som vist i følgende eksempel.

![OMS dashboard](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Næste trin

- Installer [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
- Tilmeld dig [Log analyser](https://azure.microsoft.com/documentation/services/log-analytics).  
