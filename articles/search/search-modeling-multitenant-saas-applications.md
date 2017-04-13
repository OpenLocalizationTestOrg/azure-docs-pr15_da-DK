<properties
    pageTitle="Modeling Multitenancy i Azure Søg | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Få mere at vide om almindelige designs til multiprofiler SaaS programmer prøveversionen Azure søgning."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Designe mønstre for multiprofiler SaaS programmer og Azure søgning

Et multiprofiler program er en, der indeholder de samme tjenester og -funktioner til en hvilken som helst antal lejere, der ikke kan se eller dele data fra en hvilken som helst anden lejer. Dette dokument omhandler lejer isolationsniveauet strategier for multiprofiler programmer, der er oprettet med Azure søgning.

## <a name="azure-search-concepts"></a>Azure Søg begreber
Som en søgning som en tjeneste løsning kan Azure Søg udviklere til at tilføje omfattende søgning oplevelser til programmer uden administration af enhver infrastruktur eller blive ekspert i Søg. Data, der er overført til tjenesten og derefter gemt i skyen. Brug af simple mail til Azure Søg API, kan dataene derefter redigeres og søges i. En oversigt over tjenesten kan findes i [denne artikel](http://aka.ms/whatisazsearch). Før omhandler designs, er det vigtigt at forstå nogle begreber i Azure Søg.

### <a name="search-services-indexes-fields-and-documents"></a>Søgetjenester, indeks, felter og dokumenter
Når du bruger Azure søgning, abonnerer en på en _søgetjeneste_. Når dataene er overført til Azure søgning, gemmes i et _indeks_ i search-tjenesten. Der kan være et antal indeks i en enkelt tjeneste. Hvis du vil bruge de velkendte begreber over databaser, søgetjenesten med hensyn til en database mens indeks i en tjeneste med hensyn til tabeller i en database.

Hvert indeks inden for en søgetjeneste er sin egen skema, der er defineret af et antal brugerdefinerbare _felter_. Dataene føjes til en Azure søgeindeks i form af separate _dokumenter_. Hvert dokument skal overføres til et bestemt indeks og skal være den indeks skemaet. Når du søger data ved hjælp af Azure søgning, er fuld tekst søgeforespørgsler udstedes i forhold til et bestemt indeks.  Felter med hensyn til kolonner i en tabel for at sammenligne disse begreber til navnene i en database, og dokumenter med hensyn til rækker.

### <a name="scalability"></a>Skalerbarhed
En hvilken som helst Azure søgetjenesten i den almindelige [priser niveau](https://azure.microsoft.com/pricing/details/search/) kan skalere i to dimensioner: storage og tilgængelighed.
* _Partitioner_ kan føjes til øge opbevaring af en søgetjeneste.
* _Replikaer_ kan føjes til en tjeneste for at øge gennemløb af forespørgsler, der kan håndtere en søgetjeneste.

Tilføje og fjerne partitioner og replikaer på, så kapaciteten af søgetjenesten vokse med mængden data og trafik programmet krav. I rækkefølge for en søgetjeneste til at opnå en læsning [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)kræver to replikaer. Hvis en tjeneste for at opnå en skrivebeskyttet [SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/search/v1_0/), skal kræver den tre replikaer.


### <a name="service-and-index-limits-in-azure-search"></a>Grænser for tjenesten og indeks i Azure Søg
Der er et par forskellige [priser niveauer](https://azure.microsoft.com/pricing/details/search/) i Azure Søg, hver af lag har forskellige [begrænsninger og kvoter for](search-limits-quotas-capacity.md). Nogle af disse begrænsninger er på service-niveau, nogle er på indeks-niveau, og nogle er på partition-niveau.


|                                  | Grundlæggende     | Standard1   | Standard2   | Standard3   | Standard3 HD  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Maksimale replikaer hver tjeneste     | 3         | 12          | 12          | 12          | 12            |
| Maksimale partitioner hver tjeneste   | 1         | 12          | 12          | 12          | 1             |
| Maksimale Søg enheder (replikaer * partitioner) hver tjeneste | 3         | 36          | 36          | 36          | 36 (maks 3 partitioner)            |
| Maksimale dokumenter hver tjeneste    | 1 million | 180 millioner | 720 millioner | 1.4 milliarder | 600 millioner   |
| Maksimal Storage hver tjeneste      | 2 GB      | 300 GB      | 1.2 TB      | 2.4 TB      | 600 GB        |
| Maksimale dokumenter per Partition  | 1 million | 15 millioner  | 60 millioner  | 120 millioner | 200 millioner   |
| Maksimal Storage per Partition    | 2 GB      | 25 GB       | 100 GB      | 200 GB      | 200 GB        |
| Maksimale indeks hver tjeneste      | 5         | 50          | 200         | 200         | 3000 (maks 1000 indeks/partition)          |


#### <a name="s3-high-density"></a>S3 Høj tæthed '
I Azure Søg S3 priser lag er der en indstilling for tilstanden høj tætheden (HD), der er udviklet specielt til multiprofiler scenarier. I mange tilfælde er det nødvendige for at understøtte et stort antal mindre lejere under en enkelt tjeneste til at opnå fordelene ved enkle og omkostninger effektivitet.

S3 HD giver mulighed for mange små indeks skal være pakket under Administration af en enkelt søgetjeneste af handel muligheden for at skalere ud indeks ved hjælp af partitioner giver mulighed for at være vært for flere indeks i en enkelt tjeneste.

Concretely, kan have en S3 tjeneste mellem 1 og 200 indeks, der sammen kan hoste op til 1.4 milliarder dokumenter. En S3 HD dels vil tillade individuelle indeks kun gå op til 1 million dokumenter, men den kan håndtere op til 1000 indeks per partition (op til 3000 hver tjeneste) med en samlet dokumentantal 200 millioner per partition (op til 600 millioner per service).



## <a name="considerations-for-multitenant-applications"></a>Overvejelser i forbindelse med multiprofiler programmer
Multiprofiler programmer skal effektivt distribuere ressourcer mellem lejere og samtidig bevare nogle niveau af beskyttelse af personlige oplysninger mellem de forskellige lejere. Der er nogle overvejelser, når du designer arkitekturen for sådanne et program:

* _Lejer isolationsniveauet:_ Program udviklere skal tage relevante foranstaltninger for at sikre, at ingen lejere har uautoriseret eller uønsket adgang til data fra andre lejere. Ud over perspektivet data beskyttelse af personlige oplysninger kræver lejer isolationsniveauet strategier effektiv administration af delte ressourcer og beskyttelse mod støj tilstødende netværk.
* _Ressourceomkostninger i skyen:_ Som med andre programmer, skal softwareløsninger, der forblive omkostninger Konkurrentanalyse som en del af et multiprofiler program.
* _Brugervenlighed handlinger:_ Når du udvikler en multiprofiler arkitektur, er påvirkningen af programmets handlinger og kompleksitet en vigtig overvejelse. Azure Søg har en [99,9% SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Global miljøet:_ Multiprofiler programmer nødt til at fungere effektivt lejere, som er fordelt over hele verden.
* _Skalerbarhed:_ Programmet udviklere skal overveje, hvordan de afstemme mellem bevare et tilstrækkeligt lavt niveau af programmet kompleksitet og designe program tilladelse til at skalere med antallet af lejere og størrelsen af lejere data og arbejdsbyrde.

Azure søgning indeholder nogle grænser, der kan bruges til at isolere lejere data og arbejdsbyrde.

## <a name="modeling-multitenancy-with-azure-search"></a>Modellering multitenancy med Azure søgning
Hvis det er et multiprofiler scenarie programmet udvikler forbruger en eller flere tjenester til søgning og dividere deres lejere mellem tjenester, indeks eller begge. Azure Søg har et par almindelige mønstre, når modeling et multiprofiler scenarie:

1. _Indeks per lejer:_ Hver lejer har sin egen indeks inden for en søgetjeneste, der deles med andre lejere.
1. _Service per lejer:_ Hver lejer har sin egen dedikeret Azure Search-tjenesten, med tilbud om højeste niveau af data og arbejdsbelastningen adskillelse.
1. _Blanding af begge:_ Større, mere aktiv lejere, tildeles dedikeret services, mens mindre lejere tildeles individuelle indeks i delte tjenester.

## <a name="1-index-per-tenant"></a>1. indeksere per lejer
![En portrayal af indeks per lejer modellen](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

I en indeks-per-lejer model fylde flere lejere en enkelt Azure søgetjeneste hvor hver lejer har deres egne indeks.

Lejere opnå data isolationsniveauet, fordi alle søge anmodninger og dokument handlinger er udstedt på et indeksniveau i Azure Søg. I programlag, der ikke har du brug for tilstedeværelse til at dirigere de forskellige lejere trafik til de korrekte indeks og samtidig styre ressourcer på niveauet for tjenesten på tværs af alle lejere.

Attributten vigtige af indeks per lejer modellen er muligheden for, at programmet udvikler til oversubscribe kapaciteten for en søgetjeneste mellem programmets lejere. Hvis lejere har en ulige fordeling af arbejdsbyrde, kan den optimale kombination af lejere distribueres på tværs af en søgetjeneste indeks til at medtage et antal meget aktive, ressource-intensivt lejere mens fungerer samtidig en lang hale af mindre aktive lejere. At tage er manglende mulighed for modellen til at håndtere situationer, hvor hver lejer samtidig er meget aktive.

Indeks per lejer modellen grundlaget for en variable Enhedsomkostninger model, hvor en hel Azure Search-tjenesten er købte forudgående og derefter efterfølgende udfyldt med lejere. Dette giver mulighed for ubrugte kapacitet til at være angivet til prøveversioner og gratis-konti.

Indeks per lejer modellen er muligvis ikke den mest effektive for programmer med en global miljøet. Hvis et program lejere fordeles over hele verden, kan det være nødvendigt for hvert område, som kan duplikere omkostninger på tværs af hver af dem en separat tjeneste.

Azure søgning giver mulighed for skalaen på både individuelle indeksene og det samlede antal indeks til at opnå vækst. Hvis en relevante priser vælges niveau, partitioner og replikaer kan føjes til hele søgetjenesten når et enkelt indeks i tjenesten bliver for stor med hensyn til lagerplads eller trafik.

Hvis det samlede antal indeks bliver for stor til en enkelt tjeneste, har en anden tjeneste til klargøres til at tage højde for nye lejere. Hvis indeks skal flyttes mellem søgetjenester, som er tilføjet nye tjenester, har dataene fra indekset til kopieres manuelt fra et indeks til en anden som Azure søgning ikke giver mulighed for et indeks til at blive flyttet.


## <a name="2-service-per-tenant"></a>2. Service per lejer
![En portrayal af tjenesten per lejer modellen](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

I en tjeneste per lejer arkitektur har hver lejer sin egen search-tjenesten.

I denne model opnås programmet grænseværdien isolationsniveauet for dens lejere. Hver tjeneste har dedikeret lager og overførsel for håndtering af søgeanmodning samt separat API taster.

For programmer, hvor hver lejer indeholder et stort miljøet eller arbejdsbelastningen har lille afvigelse fra lejer til lejer, er den tjeneste per lejer model et godt valg som ressourcer, der ikke deles på tværs af forskellige lejere arbejdsmængder.

En tjeneste per lejer model tilbyder også fordelen ved en mere forudsigelige, faste omkostninger model. Der er ingen forudgående investering i en hel search-tjenesten, indtil der ikke er en lejer udfylde den, men omkostninger-per-lejeren er højere end en indeks-per-lejer model.

Tjenesten per lejer modellen er et effektivt valg for programmer med en global miljøet. Med geografisk er distribueret lejere er det nemt at have hver lejer tjeneste i det pågældende land.

Udfordringerne i skalering dette mønster opstår, når individuelle lejere outgrow deres tjeneste. Azure søgning understøtter i øjeblikket ikke opgradere kan priser niveauet for en søgetjeneste, så alle data, der ville have skal kopieres manuelt til en ny tjeneste.

## <a name="3-mixing-both-models"></a>3. blanding af begge modeller
En anden mønster for modeling multitenancy blanding både indeks per lejer og service per lejer strategier.

Et program største lejere kan optage dedikeret tjenester ved at blande de to mønstre, mens den lange hale af mindre aktive, mindre lejere kan optage indeks i en delt tjeneste. Denne model sikrer, at de største lejere har ensartet høj ydeevne fra tjenesten samtidig med at beskytte mindre lejere fra en hvilken som helst støj tilstødende netværk.

Dog implementere denne strategi er afhængig fremsyn at bestemme, hvilke lejere kræver en dedikeret service kontra et indeks i en delt tjeneste. Programmet kompleksitet øges med at skulle administrere begge af disse multitenancy modeller.

## <a name="achieving-even-finer-granularity"></a>Opnår endnu bedre granularitet
De ovenstående designs til model multiprofiler scenarier i Azure Søg forudsætter et ensartet omfang, hvor hver lejer er en hel forekomst af et program. Programmer kan dog nogle gange kan håndtere mange mindre områder.

Hvis tjenesten per lejer og indeks per lejer modeller ikke er tilstrækkeligt små områder, er det muligt at model et indeks for at opnå en endnu bedre graden af granularitet.

Hvis du vil have et enkelt indeks, der fungerer anderledes for anden klient slutpunkter, kan et felt føjes til et indeks, der angiver, at en bestemt værdi for hver mulige klient. Hver gang en klient kalder Azure Søg for at forespørge eller ændre et indeks Angiver koden fra klientprogrammet den relevante værdi for det pågældende felt ved hjælp af Azure Søg [filter](https://msdn.microsoft.com/library/azure/dn798921.aspx) egenskab på Forespørgselsestid.

Denne metode kan bruges til at opnå funktionaliteten i separate brugerkonti, separate tilladelsesniveauer, og endda helt adskille programmer.

## <a name="next-steps"></a>Næste trin
Azure søgning er et overbevisende valg for mange programmer, [Læs mere om tjenestens effektive funktioner](http://aka.ms/whatisazsearch). Når evaluering af de forskellige design mønstre for multiprofiler programmer, skal du overveje de [forskellige priser niveauer](https://azure.microsoft.com/pricing/details/search/) og de respektive [grænser](search-limits-quotas-capacity.md) for bedste skræddersy Azure Søg til at passe ind arbejdsbelastninger og arkitekturer af alle størrelser.

Hvis du har spørgsmål om Azure søgning og multiprofiler scenarier kan omdirigeres til azuresearch_contact@microsoft.com.
