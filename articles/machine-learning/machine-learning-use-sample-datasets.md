<properties
    pageTitle="Bruge datasæt eksempel i Machine Learning Studio | Microsoft Azure"
    description="Beskrivelser af de datasæt, der bruges i eksempel modeller, der er inkluderet i ML Studio. Du kan bruge disse eksempel datasæt til din forsøg."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Bruge datasæt eksempel i Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Når du opretter et nyt arbejdsområde i Azure Machine Learning, medtages et antal eksempel datasæt og forsøg som standard. Mange af disse eksempel datasæt anvendes af eksempel-modeller i [Azure Cortana Intelligence galleriet](http://gallery.cortanaintelligence.com/), og andre er inkluderet som eksempler på forskellige typer data, der typisk bruges i machine learning.

Nogle af disse datasæt, der er tilgængelige i Azure Blob-lager. Tabellen nedenfor indeholder et direkte link til disse datasæt. Du kan bruge disse datasæt til din forsøg ved hjælp af [Importdata] [ import-data] modul.

Resten af disse eksempel datasæt er angivet under **Gemt datasæt** i paletten modul til venstre for forsøg lærredet, når du åbner eller opretter en ny forsøg i ML Studio.
Du kan bruge en af disse datasæt i dit eget forsøg ved at trække den til din forsøg lærred.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Navn på datasæt</th>
  <th align=left>Beskrivelse af datasæt</th>
</tr>

<tr>
  <td valign=top>Voksne tælling indtægter binære klassifikation datasæt</td>
  <td valign=top>
Et undersæt af 1994 tælling databasen, ved hjælp af arbejde voksne over alderen på 16 med et justerede indtægter indeks over > 100.<p> </p><b>Format:</b> klassificere personer, der bruger demografiske data til at forudsige, om en person, der giver mere end 50K om året.<p> </p><b>Relaterede opslag:</b> Kohavi, R., Becker, b, (1996). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Airport koder datasæt</td>
  <td valign=top>
USA airport koder.<p> </p>Dette dataset indeholder en række for hver USA airport, give airport id-nummer og navn sammen med placering by og stat.
  </td>
</tr>

<tr>
  <td valign=top>Bil prisdata (Raw)</td>
  <td valign=top>
Oplysninger om biler ved og -model, herunder prisen funktioner som antallet af flasker og MPG samt en forsikring risikoen resultat.<p> </p>Risiko karakteren er tilknyttet automatisk pris først og derefter justeret for den faktiske risiko i en proces, der er blevet konstateret, at aktuarer som symboling. En værdi på + 3 angiver, at automatisk er ustabil, og en værdi af-3, at den er sandsynligvis ret sikkert.<p> </p><b>Format:</b> forudsige risikoen karakteren af funktioner, ved hjælp af regression eller multivariate klassificering. <p> </p><b>Relaterede opslag:</b> Schlimmer, J.C. (1987). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Cykler leje UCI datasæt</td>
  <td valign=top>
UCI cykler leje datasæt, der er baseret på reelle data fra kapital Bikeshare virksomhed, der fører en cykler leje netværk i Washington DC.<p> </p>Datasættet har én række for hver time i hver dag i 2011 og 2012, i alt 17,379 rækker. Området af hver time cykler husleje er fra 1 til 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Billede af Bill Gates RGB</td>
  <td valign=top>
Offentligt tilgængelige billedfil konverteres til CSV-data.<p> </p>Koden for at konvertere billedet er angivet på siden <strong>farve quantization ved hjælp af K-betyder klynge</strong> model detaljer.
  </td>
</tr>

<tr>
  <td valign=top>Overvågning donation data</td>
  <td valign=top>
Et undersæt af data fra databasens overvågning bidragyder af overvågning Transfusion Service Center Hsin Chu by, Taiwan.<p> </p>Bidragyder data omfatter måneder siden sidste donation), og hyppighed, eller det samlede antal donationer, tid siden sidste donation og mængden af overvågning doneret.<p> </p><b>Format:</b> målet er at forudsige via klassifikation af, om bidragyderen doneret overvågning i marts 2007, hvor 1 angiver en bidragyder under målperioden og 0 en ikke-bidragyder. <p> </p><b>Relaterede opslag:</b> Yeh, I.C., (2008). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige <p> </p>Yeh, jeg-Cheng Yang konge-Jang og igeringssprog, og tryk på-Ming "Knowledge registrering på RFM model ved hjælp af Bernoulli sekvens" ekspert systemer med programmer, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Adressekartotek anmeldelser fra Amazon</td>
  <td valign=top>
Anmeldelser af bøger i Amazon, tages fra webstedet amazon.com ved University of Pennsylvania forskere (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">synspunkt</a>). Se papiret opslag "biografier, Bollywood, udligger bokse og Blenders: domæne tilpasning om synspunkt klassifikation" ved John Blitzer, Markér Dredze og Fernando Pereira; Tilknytning af Computational Linguistics (ACL) 2007.<p> </p>Det oprindelige datasæt har 975K anmeldelser med bedømmelser 1, 2, 3, 4 eller 5. Anmeldelser blev skrevet på engelsk og er fra tidsperioden 1997-2007. Dette dataset har været ned prøver at 10K anmeldelser.
  </td>
</tr>

<tr>
  <td valign=top>Bryst kræft data</td>
  <td valign=top>
En af tre kræft beslægtede datasæt, der leveres af den Oncology Institute, der vises ofte i machine learning materiale. Kombinerer diagnostiske oplysninger med funktioner fra laboratorium analyse af til 300 vævsprøver.<p> </p><b>Format:</b> klassificere typen kræft, baseret på 9 attributter, hvoraf nogle er lineære og nogle er kategoriske. <p> </p><b>Relaterede opslag:</b> Wohlberg, W.H., gade, W.N. og Mangasarian, O.L. (1995). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Bryst kræft funktioner <td valign=top>
Datasættet indeholder oplysninger om 102K mistænkelige områder (kandidater) af halvgennemsigtigt billeder, hver beskrevet ved 117 funktioner. Funktionerne, der er beskyttet, og deres betydning kan få adgang ikke ved datasæt forfatteren (Siemens sundhedssektoren). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Bryst kræft oplysninger</td>
  <td valign=top>
Datasættet indeholder ekstra oplysninger for hvert område for mistænkelige halvgennemsigtigt billede. Hvert eksempel indeholder oplysninger (f.eks., etiket, patienter-ID, koordinater af programrettelse i forhold til hele billedet) om nummeret på den tilsvarende række i datasættet bryst kræft funktioner. Hver tålmodig har et antal eksempler. Nogle eksempler er positiv patienter, der har en kræft, og nogle er negative. For patienter, der ikke har en kræft, er alle eksempler negative. Der er 102K eksempler i datasættet. Datasættet forkærlighed 0,6% af punkterne er positivt, resten er negative. Datasættet blev gjort tilgængelige ved Siemens sundhedssektoren.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>CRM Appetency etiketter delt</td>
  <td valign=top>
Etiketter fra KDD Cup 2009 kunde relation forudsigelse udfordring (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>CRM transportspand etiketter delt</td>
  <td valign=top>
Etiketter fra KDD Cup 2009 kunde relation forudsigelse udfordring (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>CRM Dataset delt</td>
  <td valign=top>
Disse data kommer fra KDD Cup 2009 kunde relation forudsigelse udfordring (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>Datasættet indeholder 50K kunder fra fransk Telecom virksomhed Orange. Hver kunde har 230 anonyme funktioner, 190 hvoraf er numeriske og 40 er kategoriske. Funktionerne er meget sparse.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>CRM Upselling etiketter delt</td>
  <td valign=top>
Etiketter fra KDD Cup 2009 kunde relation forudsigelse udfordring (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Energi effektivitet Regression data</td>
  <td valign=top>
En samling af simuleret energi profiler, baseret på 12 anden bygning figurer. Bygninger er forskellige med hensyn til opdelte ved 8 funktioner, som ruder område, ruder område fordeling og retning.<p> </p><b>Format:</b> bruge regression eller klassificering til at forudsige energieffektiviteten vurdering baseret som en af to reelle valgmuligheder svar. For flere klasse klassificering, er afrunde variablen svar til det nærmeste heltal. <p> </p><b>Relaterede opslag:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Flight forsinkelser Data</td>
  <td valign=top>
Passager flight ydelsesdata tages fra samlingen TranStats data for USA afdeling af transport (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">Til tiden</a>) på tid.<p> </p>Datasættet dækker tidsperiode April-oktober 2013. Før du overfører til Azure ML Studio, blev datasættet behandlet, på følgende måde:<ul><li>Datasættet er blevet filtreret dækker kun de 70 det mest optagne lufthavne i viser kontinental USA</li><li>Annullerede fly, flyver er blevet mærket som forskydes med mere end 15 minutter</li><li>Omkørselsafstanden fly, flyver er blevet filtreret</li><li>Følgende kolonner er markeret: år, måned, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, annulleret</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Flight til tiden ydeevne (Raw)</td>
  <td valign=top>
Poster med fly flight ankomster og afgange i USA fra oktober 2011.<p> </p><b>Format:</b> forudsige flyforsinkelser. <p> </p><b>Relaterede opslag:</b> fra USA afdeling af transport <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Skov udløses data</td>
  <td valign=top>
Indeholder vejrudsigt data, som temperatur og luftfugtighed indeks og vindhastighed fra et område i nordøst Portugal, kombineres med poster af typen af skov brand.<p> </p><b>Format:</b> dette er en svært regression opgave, hvor målet er at forudsige området brændte af skov brand. <p> </p><b>Relaterede opslag:</b> Cortez, P., og Morais, A. (2008). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige <p> </p>[Cortez og Morais, 2007] P. Cortez og A. Morais. En Data Mining fremgangsmåde til at forudsige skov udløses ved hjælp af meteorologiske Data. I J. Neves, m F. Santos og J. Machado Eds., nye tendenser i kunstig Intelligence, procedurer i den 13 EPIA 2007 – portugisiske konference om kunstig Intelligence, December, 523-Guimarães, Portugal, sider 512, 2007. APPIA, ISBN-13 978-989-95618-0-9. Findes på: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Tysk kreditkort UCI datasæt</td>
  <td valign=top>
UCI Statlog (tysk kreditkort) datasæt (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + tysk + kredit + Data</a>), ved hjælp af german.data-fil.<p> </p>Datasættet klassificerer personer, der er beskrevet i en række attributter, som lav eller høj kreditrisici. Hvert eksempel repræsenterer en person. Der er 20 funktioner, både numeriske og kategoriske, og en binær etiket (kredit risikoen værdi). Høj kredit risikoen poster har etiket = 2, lav kredit risikoen poster har etiket = 1. Omkostningerne for misclassifying en lav risiko eksempel som høj er 1, mens omkostninger for misclassifying en høj risiko eksempel som lav er 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>IMDB filmtitler</td>
  <td valign=top>
Datasættet indeholder oplysninger om film, der er klassificeret i Twitter tweets: IMDB film ID, film navn og genre, fremstilling år. Der findes 17K film i datasættet. Datasættet blev introduceret i papiret "S. Dooms, T. De Pessemier og L. Martens. MovieTweetings: en vurdering af ansøger Dataset film der indsamles fra Twitter. Workshop på Crowdsourcing og mennesker beregning til anbefalet af systemer, CrowdRec på RecSys 2013".
  </td>
</tr>

<tr>
  <td valign=top>IRIS to klassedata</td>
  <td valign=top>
Dette er måske bedst kendt databasen til at blive tilføjet i mønster talegenkendelse materiale. Datasættet er relativt lille, der indeholder 50 eksempler på bladet målinger fra tre iris Sorter.<p> </p><b>Format:</b> forudsige iris typen fra målene.  <p> </p><b>Relaterede opslag:</b> Fisher, R.A. (1988). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Film Tweets</td>
  <td valign=top>
Datasættet er en udvidet version af film Tweetings datasættet. Datasættet har 170K bedømmelser for film, udtrækkes fra ordentligt strukturerede tweets på Twitter. Hver forekomst repræsenterer et tweet og er en tupel: bruger-ID, IMDB film-ID, bedømmelse, tidsstempel, numer af Favoritter for denne tweet og antallet af retweets af denne tweet. Datasættet blev gjort tilgængelige ved A. sagde, S. Dooms, B. Loni og D. Tikk for anbefalet af systemer udfordring 2014.
  </td>
</tr>

<tr>
  <td valign=top>MPG data for forskellige biler</td>
  <td valign=top>
Dette dataset er en ændret en smule version af datasættet fra biblioteket StatLib Carnegie Mellon University. Datasættet blev brugt i 1983 American statistiske tilknytning organisationens.<p> </p>Data, der viser brændstofforbrug for forskellige biler i meter i gallon sammen med oplysninger om disse antallet af flasker, program forskydning, hestekræfter, Samlet vægt og hardwareacceleration.<p> </p><b>Format:</b> forudsige brændstofforbrug baseret på 3 med flere værdier dedikeret attributter og 5 fortløbende attributter. <p> </p><b>Relaterede opslag:</b> StatLib, Carnegie Mellon University (1993). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige </td>
</tr>

<tr>
  <td valign=top>Pima Indians Diabetes binære klassifikation datasæt</td>
  <td valign=top>
Et undersæt af data fra den nationale Institute Diabetes og indhold fra og nyrer sygdomme database. Datasættet blev filtreret til at fokusere på kvinde patienter af Pima indiske arv. Data, der indeholder læge data som blodsukker og inulinsirup niveauer samt livsstil faktorer.<p> </p><b>Format:</b> forudsige om emnet har diabetes (binære klassifikation). <p> </p><b>Relaterede opslag:</b> Sigillito version (1990). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml "</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige </td>
</tr>

<tr>
  <td valign=top>Restaurant kundedata</td>
  <td valign=top>
Et sæt af metadata om kunder, herunder demografiske data og indstillinger.<p> </p><b>Format:</b> bruges sammen med de andre to restaurant datasæt, dette dataset til at undervise og teste et anbefalet af system. <p> </p><b>Relaterede opslag:</b> Bache, K. og Lichman, m (2013). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige.
  </td>
</tr>

<tr>
  <td valign=top>Restaurant funktion data</td>
  <td valign=top>
Et sæt af metadata om restauranter og deres funktioner, som mad type, spise typografi og placering.<p> </p><b>Format:</b> bruges sammen med de andre to restaurant datasæt, dette dataset til at undervise og teste et anbefalet af system. <p> </p><b>Relaterede opslag:</b> Bache, K. og Lichman, m (2013). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige.
  </td>
</tr>

<tr>
  <td valign=top>Restaurant bedømmelser</td>
  <td valign=top>
Indeholder bedømmelser afgives af brugere til restauranter på en skala fra 0 til 2.<p> </p><b>Format:</b> bruges sammen med de andre to restaurant datasæt, dette dataset til at undervise og teste et anbefalet af system. <p> </p><b>Relaterede opslag:</b> Bache, K. og Lichman, m (2013). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige.
  </td>
</tr>

<tr>
  <td valign=top>Stål Annealing med flere klasse datasæt</td>
  <td valign=top>
Dette dataset indeholder en række poster fra stål optimerende forsøg med de fysiske attributter (bredde, tykkelse, type (coil, ark osv.) den resulterende stål typer.<p> </p><b>Format:</b> forudsige en af to numeriske klasse attributter. hårdhed eller styrke. Du kan også analysere korrelationer mellem attributter.<p> </p>Ståltyper følger en sæt standard defineret af SÆ og andre organisationer. Du leder efter en bestemt 'klasse' (Klassevariablen) og ønsker at forstå de værdier, som det er nødvendigt. <p> </p><b>Relaterede opslag:</b> Sterling, D. & Buntine W. (NA). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger og Computer videnskabelige <p> </p>En praktisk vejledning til ståltyper kan findes her: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Teleskop data</td>
  <td valign=top>
Poster med høj energi gamma partikel lyseksplosioner sammen med baggrundsstøj begge simuleret ved hjælp af en Monte Carlo proces.<p> </p>Formålet med simuleringen var at forbedre nøjagtigheden af bunden-baserede atmosfæriske Cherenkov gamma teleskoper, ved hjælp af statistiske metoder til at skelne mellem det ønskede signal (Cherenkov stråling brusere) og baggrundsstøj (hadronic brusere startet af cosmic stråler i luften på øverste).<p> </p>Dataene er blevet udfyldt behandlede til at oprette en aflange klynge med long akse er et overblik hen imod midten kamera. Egenskaber for denne ellipse, (kaldes ofte Hillas parametre) er blandt de billede parametre, der kan bruges til forskelsbehandling.<p> </p><b>Format:</b> forudsige om billede af håber repræsenterer signal eller baggrund støj.<p> </p><b>Bemærkninger:</b> Simple klassifikation nøjagtigheden ikke er relevant for disse data, siden klassificering hændelsen baggrund, som den signal er dårligere end klassificering hændelsen signal som baggrund. Sammenligning af forskellige klassificeringer skal ROC grafen bruges. Sandsynligheden for at acceptere en baggrund begivenhed, som skal være signal under en af følgende tærskler: 0,01, 0,02, 0,05, 0,1 eller 0,2.<p> </p>Bemærk også, at undervurderes antallet af baggrunden hændelser (h, til hadronic brusere), mens i reelle mål klassen h eller støj repræsenterer for størstedelen af begivenheder. <p> </p><b>Relaterede opslag:</b> Bock, R.K. (1995). UCI Machine Learning lager <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, skole af oplysninger </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Vejrudsigt datasæt</td>
  <td valign=top>
Hver time overblik-baserede vejrudsigt observationer fra NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">flettede data fra 201304 til 201310</a>).<p> </p>Datatypen vejrudsigt dækker observationer fra airport vejrudsigt stationer, som dækker tidsperiode April-oktober 2013. Før du overfører til Azure ML Studio, blev datasættet behandlet, på følgende måde:<ul><li>Vejrudsigt station id'er er knyttet til tilsvarende airport id'er</li><li>Vejrudsigt stationer ikke er knyttet til de 70 det mest optagne lufthavne er blevet filtreret</li><li>Kolonnen dato er blevet opdelt i separate kolonner til år, måned og dag</li><li>Følgende kolonner er markeret: AirportID, år, måned, dag, klokkeslæt, tidszone, SkyCondition, synlighed, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, vindhastighed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipedia SP 500 datasæt</td>
  <td valign=top>
Data er hentet fra Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) baseret på artikler på hvert S & P 500 firma, der er gemt som XML-data.<p> </p>Før du overfører til Azure ML Studio, blev datasættet behandlet, på følgende måde:<ul><li>Udtrække tekst-indhold for hvert regnskab</li><li>Fjerne formatering med Virksomhedswiki</li><li>Fjerne ikke-alfanumeriske tegn</li><li>Konvertere al tekst til små bogstaver</li><li>Kendte virksomhed kategorier blev tilføjet</li></ul><p> </p>Bemærk, at for nogle virksomheder en artikel, der blev ikke fundet, så antallet af poster er mindre end 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
Datasættet indeholder kundedata og betegnelser om deres svar til en direkte postadressen campaign. Hver række repræsenterer en kunde. Datasættet indeholder 9 funktioner om bruger demografiske data og tidligere funktionsmåde og 3 etiket kolonner (besøger, konvertering, og bruger).  Besøg er en binær kolonne, der angiver, at en kunde besøgte efter den marketingkampagne, konvertering angiver en kunde købt noget og bruger er det beløb, der blev brugt.  Datasættet blev gjort tilgængelige ved Kevin Hillstrom for MineThatData e-mail-analyser og Data Mining udfordring.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Funktioner i test eksempler i RCV1 V2 Reuters nyheder datasættet. Datasættet indeholder 781K nyhedsartikler sammen med deres id'er (første kolonne i datasættet). Hver artikel tokenized, stopworded, og skyldtes. Datasættet blev gjort tilgængelige ved David. D. Larsen.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Funktioner i kursus eksempler i RCV1 V2 Reuters nyheder datasættet. Datasættet indeholder 23K nyhedsartikler sammen med deres id'er (første kolonne i datasættet). Hver artikel tokenized, stopworded, og skyldtes. Datasættet blev gjort tilgængelige ved David. D. Larsen.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
DataSet fra KDD Cup 1999 Knowledge registrering og Data Mining værktøjer konkurrence (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).<p> </p>Datasættet blev hentet og gemt i Azure Blob-lager (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) og omfatter både kurser og test af datasæt. Kursus datasættet har cirka 126K rækker og 43 kolonner, herunder etiketter. 3 kolonner er en del af Etiketoplysninger, og 40 kolonner, som består af numeriske og streng/kategoriske funktioner er tilgængelige for kursus modellen. Testdata har cirka 22,5 K teste eksempler med de samme 43 kolonner som kursus dataene.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
Emne tildelinger for nye artikler i RCV1 V2 Reuters nyheder datasættet. En nyhedsartikel til kan tildeles til flere emner. Formatet for hver række er "<topic name>  <document id> 1". Datasættet indeholder 2.6M emne tildelinger. Datasættet blev gjort tilgængelige ved David. D. Larsen.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Disse data kommer fra KDD Cup 2010 Student ydeevne evaluering udfordring (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">student ydeevne beregning</a>). De data, der bruges er Algebra_2008_2009 kursus sæt (Stamper, J., Niculescu-Mizil, A., Ritter, S. Gordon, G.J. og Koedinger, K.R. (2010). Algebra jeg 2008-2009. Udfordring datasæt ud fra KDD Cup 2010 informative Data Mining udfordring. Finde den på <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> eller <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>Datasættet blev hentet og gemt i Azure Blob-lager (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) og indeholder logfiler fra en studerende, tutoring system. De medfølgende funktioner omfatter problem-ID og dens beskrivelse, student-ID, tidsstempel og hvor mange forsøg student foretaget før løse problemet i den rigtige måde. Det oprindelige datasæt har 8.9M poster, og dette dataset er gået ned prøver til de første 100K rækker. Datasættet indeholder 23 tabulatorsepareret kolonner med forskellige typer: numerisk kategoriske, og tidsstempel.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
