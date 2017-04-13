<properties 
    pageTitle="Azure Søg ydeevne og optimering overvejelser | Microsoft Azure" 
    description="Finjuster ydeevnen i Azure Søg og konfigurere optimal skala" 
    services="search" 
    documentationCenter="" 
    authors="LiamCavanagh" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="liamca"/>

# <a name="azure-search-performance-and-optimization-considerations"></a>Azure Søg ydeevne og optimering overvejelser

En god søgeoplevelse er en nøgle til succes for mange mobile og webprogrammer. Fra ejendom, der bruges bil markedspladser til online kataloger, påvirker hurtig søgning og relevante resultater kundeoplevelsen. Dette dokument er beregnet på at Hjælp du opdager bedste fremgangsmåder til at få mest muligt ud af Azure søgning, især for avancerede scenarier med avancerede krav til skalerbarhed, flere sprog understøtter eller brugerdefineret rangering.  Desuden dette dokument beskriver interne og dækker fremgangsmåder, der arbejde effektivt i reale kunde apps.

## <a name="performance-and-scale-tuning-for-search-services"></a>Ydeevne og skalering justering for søgetjenester

Vi er alle bruges til at søgemaskiner som Bing og Google og de tilbyder høj ydeevne.  Som et resultat, når kunder bruger din søgning aktiverede internettet eller mobile programmet, forventer de samme ydeevne karakteristika.  Når du optimerer for søgeydelse, er en af de bedste fremgangsmåder til at fokusere på ventetid, som er den tid, en forespørgsel tager at fuldføre og returnere resultater.  Når du optimerer for søgning ventetid er det vigtigt at:

1. Vælg en destination ventetid (eller den maksimale mængde tid), der anmoder om en typisk søgning fører til at fuldføre.

2. Oprette og teste et reelle arbejdsbyrde mod din søgetjenesten med et realistisk datasæt til at måle disse ventetid satser.

3. Starte med en lav antal forespørgsler sekundet (QPS), og fortsæt med at øge antallet udføres i testen, før forespørgselsventetid falder til under defineret mål ventetid.  Dette er et vigtigt benchmark kan hjælpe dig med at planlægge efter skala som dit program vokser i anvendelse.

4. Når det er muligt, kan du genbruge HTTP-forbindelser.  Hvis du bruger Azure Søg .NET SDK, betyder det, at du skal genbruge en forekomst eller [SearchIndexClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchindexclient.aspx) forekomst, og hvis du bruger REST-API, skal du genbruge en enkelt HttpClient.
 
Mens du opretter disse teste arbejdsbelastninger, der er nogle karakteristika for Azure søgning skal være opmærksom på:

1. Det er muligt at opslagsnål, så mange Søg forespørger ad gangen, vil være overvældet de ressourcer, der er tilgængelige i din Azure Search-tjenesten.  Når dette sker, får du vist HTTP 503 Svarkoder.  Derfor er det bedst at starte med forskellige områder af anmodninger om søgning til at se forskellene i ventetid satser, når du tilføjer flere search-anmodninger.

2. Overførsel af indhold for Azure søgning kan påvirke den overordnede ydeevne og forsinkelse på Azure Search-tjenesten.  Hvis du forventer at sende data, mens brugere udfører søgninger, er det vigtigt at tage dette arbejdsbelastningen til konto i din test.

3. Ikke alle søgeforespørgsel udfører på samme ydeevneniveau.  For eksempel udfører et dokument opslag eller søgning forslag typisk hurtigere end en forespørgsel med et stort antal facetter og filtre.  Det er bedst at tage de forskellige forespørgsler, du forventer at se til konto, når du opbygger en test.  

4. Variation af search-anmodninger er vigtigt, fordi Hvis du udfører kontinuerligt de samme search-anmodninger, cachelagring af data begynder at foretage ydeevne flottere end den muligvis med en mere separate forespørgsel angivet.

> [AZURE.NOTE] [Visual Studio Indlæs test](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) er en god metode til at udføre benchmark-test, som du kan udføre HTTP-anmodninger, som du har brug for til udførelse af forespørgsler Azure Søg, og gør det muligt for parallelization af anmodninger.

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Skalering Azure Søg efter høj forespørgsel satser og begrænset anmodninger

Når du får for mange begrænset anmodninger eller overstiger dine mål ventetid satser fra en øget forespørgsel indlæses, kan du se for at mindske ventetid satser på en af to måder:

1. **Øge replikaer:**  En replika er som en kopi af dine data, så Azure Søg at indlæse balance anmodninger på flere kopier.  Alle justering af belastning og replikering af data på tværs af replikaer administreres af Azure Søg, og du kan ændre antallet af replikaer, der er allokeret for din tjeneste når som helst.  Du kan tildele op til 12 replikaer i en Standard søgetjeneste og 3 replikaer i en grundlæggende søgning tjeneste.  Replikaer kan tilpasses, enten fra [Azure-portalen](search-create-service-portal.md) eller via [Azure Søg management API](search-get-started-management-api.md).

2. **Øge Søg niveau:**  Azure Søg leveres i et [antal niveauer](https://azure.microsoft.com/pricing/details/search/) , og hver af disse lag indeholder forskellige niveauer af ydeevne.  I nogle tilfælde skal du muligvis så mange forespørgsler med det niveau, du bruger ikke kan tilbyde tilstrækkeligt lav ventetid satser, selvom replikaer er maxed ud.  I dette tilfælde kan du overveje at udnytte en af de højere Søg lag som det Azure Søg S3 niveau, der er velegnet til scenarier med stort antal dokumenter og meget høj forespørgsel arbejdsmængder.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Skalering Azure søgning efter langsom individuelle forespørgsler

En anden mulig grund hvorfor ventetid satser kan være langsom er fra en enkelt forespørgsel, der tager for lang tid at fuldføre.  I dette tilfælde forbedres tilføje replikaer ikke ventetid satser.  I dette tilfælde findes der to muligheder:

1. **Øge partitioner** En partition er en funktion til opdeling af dine data på tværs af ekstra ressourcer.  Derfor, når du tilføjer en anden partition bliver dine data opdelt i to.  En tredje partition opdeler indekset i tre osv.  Dette har også effekten, i nogle tilfælde langsom forespørgsler udfører hurtigere på grund af parallelization af beregning.  Der er nogle få eksempler på hvor vi har set dette parallelization fungerer meget fint med forespørgsler, der har lav selektivitet forespørgsler.  Består af forespørgsler, der svarer til mange typer dokumenter eller når faceting skal give tæller over stort antal dokumenter.  Da der er en masse beregning, der skal bruges til at give relevans for dokumenter, eller til at tælle antallet af dokumenter, kan tilføje ekstra partitioner hjælpe med at angive yderligere beregning.  

   Der kan være maksimalt 12 partitioner i Standard search-tjenesten og 1 partition i grundlæggende search-tjenesten.  Partitioner kan tilpasses, enten fra [Azure-portalen](search-create-service-portal.md) eller via [Azure Søg management API](search-get-started-management-api.md).

2. **Begrænse høj kardinalitet felter:** Et felt med høj kardinalitet består af en facetable eller filtrerbare felt, der indeholder et betydeligt antal entydige værdier, og som et resultat, det tager mange ressourcer til at beregne resultater.   For eksempel ville indstille et felt, produkt-ID eller beskrivelse som facetable/filtrerbare få for høj kardinalitet fordi de fleste af værdierne fra dokument til dokument er entydige. Når det er muligt, kan du begrænse antallet af høj kardinalitet felter.

3. **Øge Søg niveau:**  Flytte op til kan et højere niveau i Azure søgning være en anden måde at forbedre ydeevnen af langsom forespørgsler.  Hvert højere niveau indeholder også hurtigere CPU og mere hukommelse, som kan have en positiv indflydelse på forespørgslens ydeevne.

## <a name="scaling-for-availability"></a>Skalering for tilgængelighed

Replikaer ikke kun at reducere forespørgselsventetid men kan også give tilladelse til høj tilgængelighed.  Du kan forvente at være periodiske nedetid på grund af server genstarter med en enkelt replika efter softwareopdateringer eller til andre vedligeholdelse hændelser, der skal forekomme.  Som et resultat, er det vigtigt at overveje, hvis programmet kræver høj tilgængelighed af søgninger (forespørgsler) samt skriver (indeksering hændelser).  Azure søgning indeholder SLA muligheder på alle betalt Søg tilbud med følgende attributter:

- 2 replikaer for høj tilgængelighed af skrivebeskyttet arbejdsmængder (forespørgsler)
- 3 eller flere replikaer for høj tilgængelighed af læse-og skriveadgang arbejdsmængder (forespørgsler og indeksering)

Besøg [Azure Søg serviceaftale](https://azure.microsoft.com/support/legal/sla/search/v1_0/)kan finde flere oplysninger om dette.

Da replikaer kopier af dine data, kan har du flere replikaer Azure Søg til at machine genstarter og vedligeholdelse mod én replika ad gangen samtidig forespørgsler fortsat skal udføres på de andre replikaer.  Derfor skal du også overveje, hvordan denne nedetid kan påvirke de forespørgsler, der nu skal udføres på en mindre kopi af dataene.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Skalering geografisk distribueret arbejdsmængder og giver geografisk redundans

Du finder geografisk distribueret arbejdsbelastninger, at brugere, der er placeret langt fra det datacenter, hvor din Azure Search-tjenesten er hostet vil have højere ventetid satser.  Derfor er det ofte vigtigt, at du har flere tjenester til søgning i områder, der er tættere på disse brugere.  Azure søgning indeholder ikke i øjeblikket en automatisk metode til replikering af geografisk Azure søgeindeks på tværs af områder, men der er nogle teknikker, der kan bruges, som kan gøre denne proces, der er nem at implementere og administrere. Disse er beskrevet i de næste par afsnit.

Formålet med en geografisk distribueret sæt søgetjenester er at have to eller flere indeks, der er tilgængelige i to eller flere områder, hvor en bruger bliver dirigeret til Azure søgetjenesten, der indeholder den laveste ventetid, som det ses i dette eksempel:

   ![Kryds-fanen tjenester efter område][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Holde data synkroniseret på tværs af flere Azure Søg tjenester

Der er to muligheder for at holde dine distribueret søgning synkroniseret tjenester som består af enten ved hjælp af [Azure Søg indekseringsprogram](search-indexer-overview.md) eller Push-API (også kaldet [Azure Search REST-API](https://msdn.microsoft.com/library/dn798935.aspx)).  

### <a name="azure-search-indexers"></a>Azure Søg indeks

Hvis du bruger Azure Søg indekseringsprogram, importerer du allerede dataændringer fra en central datastore som Azure SQL DB eller DocumentDB. Når du opretter en ny søgning tjeneste, du blot også oprette en ny Azure Søg indekseringsprogram for den pågældende tjeneste, der peger på denne samme datastore. Måde, når der kommer nye ændringer ind i datalager, bliver de derefter indekseret ved forskellige indeks.  

Her er et eksempel på hvad denne arkitektur vil se ud.

   ![Enkelt datakilde med fordelt indekseringsprogram og service kombinationer][2]


### <a name="push-api"></a>Push-API 
Hvis du bruger Azure Søg Push-API til at [opdatere indhold i dit søgeindeks Azure](https://msdn.microsoft.com/library/dn798930.aspx), kan du holde dine forskellige søgetjenester synkroniseret ved at trykke ændringer til alle Søg services, hver gang en opdatering er nødvendig.  Ved at gøre dette er det vigtigt at sikre, at du skal håndtere tilfælde, hvor en opdatering til én søgetjenesten mislykkes, og en eller flere opdateringer lykkes.

## <a name="leveraging-azure-traffic-manager"></a>Udnytte Azure trafik Manager

[Azure trafik Manager](../traffic-manager/traffic-manager-overview.md) kan du distribuere anmodninger om til flere geografisk findes websteder, der understøttes derefter af tjenesterne Azure søgning.  En fordel af trafik Manager er, at det kan forespørgsler Azure Søg for at sikre, at den er tilgængelig og omdirigere brugere til alternative søgetjenester i tilfælde af nedetid.  Desuden, hvis du routing search-anmodninger via Azure websteder, kan Azure trafik Manager til at indlæse balance tilfælde, hvor det offentlige websted er op, men ikke Azure søgning.  Her er et eksempel på, hvilken arkitekturen, der bruger trafik Manager.

   ![Kryds-fanen tjenester efter område, med central trafik Manager][3]

## <a name="monitoring-performance"></a>Overvågning af ydeevnen

Azure søgning giver mulighed for at analysere og overvåge ydeevnen for din tjeneste via [Søgning trafik Analytics (STA)](search-traffic-analytics.md). Gennem STA, kan du eventuelt logge individuelle søgehandlinger samt aggregeret målepunkter en Azure-lager-konto, kan derefter behandles til analyse eller visualized i Power BI.  Brug af STA målepunkter, kan du gennemgå præstationstal som gennemsnitlige antal forespørgsler eller forespørgsel svar klokkeslæt.  Desuden kan handlingen logføring du få flere detaljer i specifik søgning handlinger.

STA er et værdifuldt værktøj at forstå ventetid satser fra Azure Søg perspektivet.  Da forespørgsel ydeevne målepunkter logget er baseret på den tid, en forespørgsel tager skal behandles fuldt ud i Azure søgning (fra den tid, det der anmodes om til, når det bliver sendt ud), er du kunne bruges til at bestemme, om ventetid problemer er fra Azure Search-tjenesten side eller uden for tjenesten, såsom fra netværksventetid.  

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om de priser niveauer og tjenester grænser for hver af dem, i [tjenesten begrænsninger i Azure Søg](search-limits-quotas-capacity.md).

Gå til [Planlægning af kapacitet](search-capacity-planning.md) til at få mere at vide om kombinationer af partition og replika.

Se følgende video for mere detaljeret visning af ydeevnen og for at se nogle demonstrationer af, hvordan du implementerer de optimeringer, der er beskrevet i denne artikel:

> [AZURE.VIDEO azurecon-2015-azure-search-best-practices-for-web-and-mobile-applications]

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png