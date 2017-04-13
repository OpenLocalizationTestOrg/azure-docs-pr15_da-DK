<properties 
    pageTitle="Almindelige DocumentDB use cases | Microsoft Azure" 
    description="Få mere at vide om øverst fem use cases, til DocumentDB: bruger genereres indhold, hændelseslogføring, katalogdata, brugerdata indstillinger og Internet af ting (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="hawong"/>

# <a name="common-documentdb-use-cases"></a>Almindelige DocumentDB Brug sager
I denne artikel indeholder en oversigt flere almindelige use cases for DocumentDB.  Anbefalinger i denne artikel fungere som et udgangspunkt, når du udvikler dit program med DocumentDB.   

Når du har læst i denne artikel, vil du kunne besvare spørgsmål, der er følgende: 
 
- Hvad er de almindelige use cases for DocumentDB?
- Hvad er fordelene ved at bruge DocumentDB som en bruger genereret indhold store?
- Hvad er fordelene ved at bruge DocumentDB som et katalogdata gemmes?
- Hvad er fordelene ved at bruge DocumentDB som en hændelseslog gemme?
- Hvad er fordelene ved at bruge DocumentDB som en bruger indstillinger data gemmes?
- Hvad er fordelene ved at bruge DocumentDB som et datalager til Internet af ting (IoT) systemer?

## <a name="common-use-cases-for-documentdb"></a>Almindelige brug sager for DocumentDB
Azure DocumentDB er en generelle formål NoSQL database, der bruges i en lang række programmer og use cases. Det er et godt valg til alle programmer, der skal lav rækkefølge af millisekunder får svar, og skal skalere hurtigt. Følgende er nogle attributter i DocumentDB, som gør det velegnet til høj ydeevne programmer.

- DocumentDB partitioner oprindeligt dataene til høj tilgængelighed og skalerbarhed.
- Documentdbs har SSD sikkerhedskopier storage med lav ventetid rækkefølge af millisekunder svar gange.
- Documentdbs understøttelse af konsistens niveauer som eventuel, session og afgrænset staleness giver mulighed for lav omkostninger til performance-bredde-forhold. 
- DocumentDB har en fleksible data-venlig priser model, målere lager og overførselshastighed uafhængigt af hinanden.
- Documentdbs reserveret overførselshastighed modellen giver dig til at tro med hensyn til antallet af læser/skriver i stedet for CPU/hukommelse/IOP'er af den underliggende hardware.
- Documentdb's design kan du tilpasse til massive anmodning enheder i den rækkefølge, milliarder af anmodninger om dagen.

Følgende attributter er særligt nyttige, når det drejer sig om internettet, mobil, gaming og IoT programmer, der skal lav svar gange og har brug for til at håndtere store mængder læsning og skrivning. 

## <a name="user-generated-content"></a>, Der genereres brugerindhold
En fælles use case for DocumentDB er at lagre og forespørgsel bruger genereres indhold (UGC) til internettet og mobile programmer, især sociale medieprogrammer.  Nogle eksempler på UGC er chatte, tweets, blogindlæg, bedømmelser og kommentarer.  UGC i programmerne på sociale medier er ofte en blanding af fri tekst, egenskaber, mærker og relationer, der ikke er afgrænset af fast struktur.   

Indhold som Chat, kommentarer og indlæg, der kan gemmes i DocumentDB uden transformationer eller komplekse objekt til relationelle tilknytning lag.  Dataegenskaber kan tilføjes eller ændres nemt for at imødekomme behov, som udviklere forbedrer over programkode, således at fremme hurtig udvikling.  

Programmer, der integreres med forskellige sociale netværk skal reagere på ændrede skemaer fra disse netværk.  Som data automatisk indekseret som standard i DocumentDB, er data klar til at være forespørges når som helst.  Det vil sige, programmerne har mulighed for at hente prognoser ud fra deres respektive behov.       

Mange af de sociale programmer kører skaleres til globale og kan det udvise uventede brugsmønstre.  Fleksibilitet i skalering datalager er afgørende, mens programlag skaleres til at matche brugen behov.  Du kan skalere ud ved at tilføje yderligere datapartitioner under en DocumentDB-konto.  Desuden kan du også oprette flere DocumentDB konti på tværs af flere områder. Du kan finde DocumentDB tjenesten område kører [Azure områder](https://azure.microsoft.com/regions/#services).   

## <a name="catalog-data"></a>Katalogdata
Katalog data brugsscenarier involverer lagring og forespørgsler en række attributter for objekter som personer, steder og produkter.  Nogle eksempler på katalogdata er brugerkonti, produktkataloger, enhed registre for IoT og faktura af materialer systemer.  Attributter for disse data kan variere og kan ændres med tiden til at passe ind programmet krav.  

Overvej et eksempel med et produktkatalog til en bil dele leverandør. Hver del kan have sin egen attributter almindelige attributter, som alle webdele deler.  Attributter for en bestemt del kan desuden ændre det følgende år, når en ny model frigives.  Som et JSON dokumentlager DocumentDB understøtter fleksible skemaer og gør det muligt at repræsentere data med indlejrede egenskaber, og dermed det er velegnet til lagring af produktdata katalog.       

## <a name="logging-and-time-series-data"></a>Logføring og tidsserie data
Programmet logføring er ofte udledes i store mængder og kan har forskellige attributter baseret på den installerede programversion eller komponent logføring begivenheder.  Logdata er ikke afgrænset af komplekse relationer eller faste strukturer. Stadig flere bevaret logdata i JSON-formatet, da JSON er lette og nemt for mennesker at læse.
   
Der er som regel to overordnede use cases, der er relateret til hændelseslog data.  Første use case er at udføre ad hoc-forespørgsler over et undersæt af data i forbindelse med fejlfinding.  Under fejlfinding af hentes et undersæt af data først fra loggene, typisk ved tidsserie.  Derefter udføres analyse ned ved at filtrere datasæt med fejlniveauer eller fejlmeddelelser. Dette er er hvor gemme hændelseslogfiler i DocumentDB en fordel. Logdata, der er gemt i DocumentDB indekseret automatisk som standard, og dermed er klar til forespørges når som helst. Desuden kan logdata blive bevaret på tværs af datapartitioner som en tidsserie. Ældre logfiler kan rulles til kolde lager per din opbevaringspolitik.          

Den anden use case omfatter længerevarende data analytics job, der udføres offline over en stor mængde logdata.  Af denne use case som eksempler kan nævnes server tilgængelighed analyse, programmet fejl analyse og clickstream dataanalyse.  Hadoop bruges typisk til at udføre disse typer analyser.  Med Hadoop Connector for DocumentDB funktionen DocumentDB databaser som datakilder og dræn for gris, Hive og kort/reducere sager. Hadoop-forbindelsen til DocumentDB vide, skal du se [køre et Hadoop-job med DocumentDB og HDInsight](documentdb-run-hadoop-with-hdinsight.md).      

## <a name="gaming"></a>Gaming
Database niveauet er en vigtig komponent i spilleapplikationer. Moderne spil udføre grafiske behandling i mobile/console-klienter, men er afhængige af skyen til at levere tilpassede og personlige indhold som i game statistik, integration af sociale medier og høj karakteren leaderboards. Spil kræver meget lav latenstider for læser og skriver til at levere en engagerende i-game oplevelse, og database niveauet skal håndtere højeste og laveste værdier i anmodning satser under ny game startes og funktionsopdateringer.

DocumentDB bruges af massive skala games som [feltet gå inaktive: ingen mand overblik](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) ved [Næste spil](http://www.nextgames.com/), og [Halo 5: værger](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). I begge use cases, de vigtigste fordele ved DocumentDB er følgende:

- DocumentDB gør det muligt for ydeevnen skaleres op eller ned elastically. Dette giver mulighed for spil til at håndtere opdaterer profil og statistik fra massevis til millioner af samtidige spillere ved at gøre et enkelt API-opkald.
- DocumentDB understøtter millisekunder læser og skriver til at undgå eventuelle LAG'er under game Afspil.
- Documentdbs automatisk indeksering giver mulighed for at filtrere i forhold til flere forskellige egenskaber i realtid, f.eks. finde afspillere efter deres interne player-id'er, eller deres GameCenter, Facebook, Google-id'er eller forespørgsel, der er baseret på player medlemskab af en guild. Dette er mulige uden opbygning af komplekse indeksering eller sharding infrastruktur.
- Sociale funktioner, herunder i game chat-meddelelser, player guild medlemskaber, udfordringer, der er fuldført, høj karakteren leaderboards og sociale grafer er nemmere at implementere med en fleksibel skema.
- DocumentDB som en administreret platform-som-en-tjeneste (PaaS) er påkrævet minimale konfiguration og administration arbejde for at tillade til hurtig gentagelse, og reducere tid på markedet.


## <a name="user-preferences-data"></a>Brugerdata indstillinger
I dag, leveres de fleste moderne web og mobile programmer med komplekse visninger og oplevelser. Disse visninger og oplevelser er som regel dynamiske, køkken-og brugerindstillinger eller humør og branding behov.  Det vil sige, programmer skal kunne hente personlige indstillinger effektivt for at gengive elementer i Brugergrænsefladen og oplevelser hurtigt. 

JSON er en effektiv format til at repræsentere Brugergrænsefladen layout data, som det er ikke kun lette, men også kan nemt fortolkes af JavaScript.  DocumentDB indeholder tunable konsistens sikkerhedsniveauer, hvor hurtigt læser med lav ventetid skriver. Det vil sige, gemmer Brugergrænsefladen layout data, herunder personlige indstillinger som JSON dokumenter i DocumentDB er et effektivt middel til at få disse data på tværs af netværket.

## <a name="iot-and-device-sensor-data"></a>IoT og enhed føler data
IoT Brug tilfælde dele ofte nogle mønstre i hvordan de indtager, behandler og gemmer data.  Først skal Tillad disse systemer data indtag, der kan indtager indstilling data fra enheden sensorer af forskellige landestandarder.  Derefter disse systemer behandle og analysere streaming data for at udlede realtid indsigt. Og sidst men ikke mindst de fleste Hvis du ikke vil til sidst lander alle data i et datalager for adhoc forespørgsel og offline analyser.    

Microsoft Azure indeholder mange tjenester, der kan bruges til IoT use cases.  Azure IoT tjenester, der er et sæt af tjenester, herunder Azure begivenhed Hubs, Azure DocumentDB, Azure Stream Analytics, Azure meddelelse-Hub, Azure Machine Learning, Azure HDInsight og PowerBI. 

Indstilling data kan være optagelse af Azure begivenhed Hubs, som det tilbyder høj overførselshastighed data indtagelse med lav ventetid. Kan funneled optagelse data, der skal behandles for realtid indsigt til Azure Stream Analytics for realtid analyser. Data kan indlæses i DocumentDB til ad hoc-forespørgsler. Når dataene er indlæst i DocumentDB, data, er klar til at være forespørges.  Dataene i DocumentDB kan bruges som referencedata som en del af realtid analyser. Desuden kan data yderligere raffineres og behandles ved at oprette forbindelse DocumentDB data til HDInsight for gris, Hive eller kort/reducere sager.  Elegant data indlæses derefter tilbage til DocumentDB til rapportering.   

Du kan finde et eksempel IoT løsning ved hjælp af DocumentDB, EventHubs og Storm, [hdinsight-storm-eksempler lager på GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Se [oprette Internet af dine ting](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx)kan finde flere oplysninger om Azure tilbud til IoT.

## <a name="next-steps"></a>Næste trin
 
For at komme i gang med DocumentDB, kan du oprette en [konto](https://azure.microsoft.com/pricing/free-trial/) og derefter følge vores [læringssti](https://azure.microsoft.com/documentation/learning-paths/documentdb/) for at få mere at vide om DocumentDB og finde de nødvendige oplysninger. 

Eller, hvis du vil læse mere om kunder, der bruger DocumentDB, der er tilgængelige på følgende kunde historier:

- [Næste spil](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Walking inaktive: mand 's overblik game soars til #1 understøttes af Azure DocumentDB.
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Hvordan implementeret Halo 5 sociale spil ved hjælp af Azure DocumentDB.
- [Cortana Analytics galleriet](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics galleri – en bygget på Azure DocumentDB SVG community-webstedet.
- [Lynhurtigt](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Foranstillede Integrator får multinationale virksomheder globale indsigt i minutter med fleksible skyen teknologier.
- [Nyheder Republik](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Tilføje intelligence nyheder til at informere med formål for optaget borgere. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). For ensartet farve over hele verden slå overordnede mærker til SGS. Og SGS omdannes til Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Global fyldtegn Telenor bruger skyen til at flytte med hastigheden for en start. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Store fremtidige kører på hurtig søgning og nemt strømmen af data.
