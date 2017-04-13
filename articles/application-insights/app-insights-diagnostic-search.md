<properties 
    pageTitle="Ved hjælp af diagnosticering søgning | Microsoft Azure" 
    description="Søge og filtrere individuelle konkurrencer, anmodninger, og log sporinger." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/09/2016" 
    ms.author="awills"/>
 
# <a name="using-diagnostic-search-in-application-insights"></a>Ved hjælp af diagnosticering søgning i programmet indsigt

Diagnosticering søgning er en funktion i [Programmet indsigt] [ start] , at du bruger til at finde og udforske individuelle telemetri elementer som sidevisninger undtagelser, eller web anmodninger. Og du kan få vist, log sporinger og begivenheder, du har indkodet.

## <a name="where-do-you-see-diagnostic-search"></a>Hvor kan du se diagnosticering søgning?


### <a name="in-the-azure-portal"></a>På portalen Azure

Du kan åbne diagnosticering Søg eksplicit:

![Åbn diagnosticering søgning](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


Det åbner også, når du klikker på gennem nogle diagrammer og gitter elementer. I dette tilfælde er dens filtre allerede angivet til at fokusere på typen element, du har valgt. 

Eksempelvis hvis dit program er en webtjeneste, viser bladet oversigt over et diagram over lydstyrken for anmodninger. Klik på den, og du får til en mere detaljeret diagram med en liste, der viser, hvor mange anmodninger om der er foretaget for hvert URL-adresse. Klik på en hvilken som helst række, og du får en liste over de enkelte anmodninger for denne URL-adresse:

![Åbn diagnosticering søgning](./media/app-insights-diagnostic-search/07-open-from-filters.png)


Hoveddelen af diagnosticering søgning er en liste over telemetri elementer - anmodninger til serveren, siden visninger, brugerdefinerede hændelser, du har kodet osv. Øverst på listen er en oversigt over diagram, der viser antallet af hændelser over tid.

Hændelser, der typisk vises i diagnosticering søgning før de vises i metriske explorer. Selvom bladet opdaterer selve med intervaller, kan du klikke på Opdater, hvis du venter for en bestemt hændelse.


### <a name="in-visual-studio"></a>I Visual Studio

Åbn vinduet Søg i Visual Studio:

![](./media/app-insights-diagnostic-search/32.png)

Vinduet Søg har de samme funktioner som webportalen:

![](./media/app-insights-diagnostic-search/34.png)


## <a name="sampling"></a>Udvalg

Hvis din app genereres en masse telemetri (og du bruger ASP.NET SDK version 2.0.0-beta3 eller nyere), modulet tilpasset stikprøver, hvor der automatisk reducere lydstyrken, der sendes til portalen ved at sende en repræsentant brøkdel af begivenheder. Dog hændelser, der er relateret til den samme anmodning markeres eller ikke er markeret som en gruppe, så du kan navigere mellem relaterede begivenheder. 

[Få mere at vide om udvalg](app-insights-sampling.md).


## <a name="inspect-individual-items"></a>Undersøge individuelle elementer

Vælg en hvilken som helst telemetri elementet for at se nøglefelter og relaterede elementer. Hvis du vil se det samlede sæt af felter, skal du klikke på "...". 


![Klik på ny arbejdsopgave, Rediger felterne, og klik derefter på OK.](./media/app-insights-diagnostic-search/10-detail.png)

Brug almindelig strenge (uden jokertegn) til at finde det samlede sæt af felter. De tilgængelige felter afhænger af typen telemetri.

## <a name="create-work-item"></a>Oprette arbejdsopgave

Du kan oprette en fejl i Visual Studio Team Services med oplysninger fra en hvilken som helst telemetri element. 

![Klik på ny arbejdsopgave, Rediger felterne, og klik derefter på OK.](./media/app-insights-diagnostic-search/42.png)

Første gang du gør dette, bliver du bedt om at konfigurere et link til dit Team Services-kontoen og project.

![Angiv URL-adressen på dit Team Services-server og navnet på projektet, og klik på godkende](./media/app-insights-diagnostic-search/41.png)

(Du kan også finde bladet konfiguration under Indstillinger > arbejdselementer.)

## <a name="filter-event-types"></a>Filtrere typer begivenheder

Åbn bladet Filter, og vælg typerne begivenhed, du vil have vist. (Hvis du senere, du vil gendanne de filtre, som du har åbnet bladet, skal du klikke på Nulstil).


![Vælg Filter, og vælg telemetri typer](./media/app-insights-diagnostic-search/02-filter-req.png)


Typerne begivenhed er:

* **Sporing** - diagnosticeringslogfiler, herunder TrackTrace, log4Net, NLog og System.Diagnostic.Trace opkald.
* **Anmode om** – HTTP-anmodninger modtaget af din serverprogram, herunder sider, scripts, billeder, typografi filer og data. Disse hændelser bruges til at oprette anmodning og svar oversigt diagrammer.
* **Sidevisning** - Telemetri, der er sendt af webklienten bruges til at oprette siden view-rapporter. 
* **Custom Event** - Hvis du har indsat opkald til TrackEvent() i ordre for at [overvåge brugen][track], du kan søge efter dem her.
* **Undtagelse** - ikke-opfanget undtagelser i serveren og dem, du logger ved hjælp af TrackException().

## <a name="filter-on-property-values"></a>Filtrere efter egenskabsværdier

Du kan filtrere begivenheder på værdierne i deres egenskaber. De tilgængelige egenskaber afhænger af typerne begivenhed, du har valgt. 

Vælg eksempelvis ud af anmodninger om med en bestemt svarkode.

![Udvide en egenskab, og vælg en værdi](./media/app-insights-diagnostic-search/03-response500.png)

Vælge nogen værdier for en bestemt egenskab har den samme effekt som vælge alle værdier. den skifter fra filtrering på denne egenskab.


### <a name="narrow-your-search"></a>Indsnævre søgningen

Bemærk, at der vises tæller til højre for filterværdier, hvor mange forekomster der er i det aktuelle filtrerede sæt. 

I dette eksempel har den Fjern, som den `Reports/Employees` anmode om resultater i fleste af 500 fejl:

![Udvide en egenskab, og vælg en værdi](./media/app-insights-diagnostic-search/04-failingReq.png)

Hvis du vil også se, hvilke andre begivenheder sker der i denne periode, kan du desuden se **Medtag begivenheder med udefineret egenskaber**.

## <a name="remove-bot-and-web-test-traffic"></a>Fjerne bot og web test trafik

Anvende filteret **reelle eller korte trafik** , og Markér **reelt tal**.

Du kan også filtrere efter **kilden til korte trafik**.

## <a name="inspect-individual-occurrences"></a>Undersøge individuelle forekomster

Føje med anmodning om navnet til sættet filter, og du kan derefter undersøge individuelle forekomster over den pågældende begivenhed.

![Vælg en værdi](./media/app-insights-diagnostic-search/05-reqDetails.png)

Vis undtagelser, der er indtruffet mens anmodningen blev behandlet for anmodning om begivenheder oplysninger.

Klik dig gennem en undtagelse for at se dens detaljer, herunder staksporing.

![Klik på en undtagelse](./media/app-insights-diagnostic-search/06-callStack.png)

## <a name="find-events-with-the-same-property"></a>Finde hændelser med samme egenskab

Finde alle elementer med den samme egenskabsværdi:

![Højreklik på en egenskab](./media/app-insights-diagnostic-search/12-samevalue.png)

## <a name="search-by-metric-value"></a>Søge efter metriske værdi

Få alle anmodninger om svartid > 5s.  Gange er repræsenteret i akser: 10 000 aksemærker = 1ms.

!["Svartid":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## <a name="search-the-data"></a>Søge efter data

Du kan søge efter ord i en af egenskabsværdierne. Dette er især nyttig, hvis du har skrevet [brugerdefinerede hændelser] [ track] med egenskabsværdier. 

Du skal måske angiver et område, som søgninger over et kortere område er hurtigere. 

![Åbn diagnosticering søgning](./media/app-insights-diagnostic-search/appinsights-311search.png)

Søge efter ord, ikke understrenge. Vilkår er alfanumeriske strenge, herunder nogle tegnsætning som '.' og '_'. Eksempel:

ord|*ikke* matches af|men disse stemmer overens
---|---|---
HomeController.About|om<br/>privat|h\*om<br/>privat\*
IsLocal|lokale<br/>er<br/>\*lokale|ISL\*<br/>islocal<br/>i\*l\*
Ny forsinkelse|w d|ny<br/>forsinkelse<br/>n\* og d\*


Her er de søgeudtryk, kan du bruge:

På eksempelforespørgsel | Effekt 
---|---
sænke|Finde alle begivenheder i det datointerval, hvis felter indeholder ordet "langsomme"
database?|Svarer til database01, databaseAB...<br/>? er ikke tilladt i starten af et søgeord.
database * |Svarer til databasen, database01, databaseNNNN<br/> * er ikke tilladt i starten af et søgeord
Apple og bananer|Find hændelser, der indeholder begge ord. Bruge capital "og" ikke "og".
Apple eller bananer<br/>Apple bananer|Find hændelser, der indeholder et udtryk. Brug "Eller", ikke "eller". < /br/ > kort formular.
Apple ikke bananer<br/>Apple-bananer|Find hændelser, der indeholder et udtryk, men ikke på den anden.<br/>Kort form.
App * og bananer-(grape pear)|Logiske operatorer og fremkommet.
"Metrisk": 0 til 500<br/>"Metrisk": 500 til * | Find hændelser, der indeholder det navngivne mål inden for værdiområdet.


## <a name="save-your-search"></a>Gemme din søgning

Når du har angivet alle de ønskede filtre, kan du gemme søgningen som en favorit. Hvis du arbejder i en organisationskonto, kan du vælge, om du vil dele den med andre gruppemedlemmer.

![Klik på favorit, Angiv navnet, og klik på Gem](./media/app-insights-diagnostic-search/08-favorite-save.png)


Se søge igen, **Gå til bladet oversigt** og åbne favoritter:

![Favoritter-felt](./media/app-insights-diagnostic-search/09-favorite-get.png)

Hvis du har gemt med Relative tidsinterval, har bladet igen åbnet de nyeste data. Hvis du har gemt med absolutte tidsinterval skal se du de samme data hver gang.


## <a name="send-more-telemetry-to-application-insights"></a>Sende flere telemetri til programmet indsigt

Ud over den out box telemetri, der sendes af programmet indsigt SDK, kan du:

* Registrere log sporinger fra din foretrukne logføring framework i [.NET] [ netlogs] eller [Java][javalogs]. Denne betyder, at du kan søge i dine log sporinger og koordinere dem med sidevisninger, undtagelser og andre begivenheder. 
* [Skrive programkode] [ track] til at sende brugerdefinerede hændelser, sidevisninger og undtagelser. 

[Lær at sende logfiler og brugerdefinerede telemetri til programmet indsigt][trace].


## <a name="questions"></a>Q & A

### <a name="limits"></a>Hvor meget data bevares?

Op til 500 begivenheder sekundet fra hvert program. Hændelser bevares til syv dage.

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hvordan kan jeg se INDLÆG data i min anmodninger til serveren?

Vi ikke kan logge INDLÆG data automatisk, men du kan bruge [TrackTrace eller log opkald][trace]. Placer INDLÆG data i parameteren meddelelse. Du kan ikke filtrere på meddelelsen, som du kan egenskaber, men størrelsesgrænsen er længere.

## <a name="add"></a>Næste trin

* [Sende logfiler og brugerdefinerede telemetri til programmet indsigt][trace]
* [Konfigurere tilgængeligheden og svartid test][availability]
* [Fejlfinding i forbindelse med][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-api-custom-events-metrics.md

 