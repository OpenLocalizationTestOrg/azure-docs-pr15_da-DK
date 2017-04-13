<properties
   pageTitle="Cachelagring vejledning | Microsoft Azure"
   description="Retningslinjer for cachelagring for at forbedre ydeevne og skalerbarhed."
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
   ms.date="07/14/2016"
   ms.author="masashin"/>


# <a name="caching-guidance"></a>Cachelagring vejledning

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

Cachelagring er en fælles teknik, der har til formål at forbedre den ydeevne og skalerbarhed af et system. Det gør dette ved at kopiere midlertidigt hyppigt data til hurtig lager, der er placeret Luk for at programmet. Hvis denne hurtige datalagring er placeret tættere på programmet end den oprindelige kilde, kan derefter cachelagring forbedre betydeligt svar sluttidspunkter for klientprogrammer ved at fungere data hurtigere.

Cachelagring er mest effektive når en klientforekomst af gentagne gange læser de samme data, især hvis alle følgende betingelser gælder for det oprindelige datalager:
- Det forbliver relativt statiske.
- Det er langsom sammenlignet med hastigheden for cachen.
- Det er underlagt en høj grad med konflikt.
- Det er langt væk, når netværksventetid kan få access til at være langsom.

## <a name="caching-in-distributed-applications"></a>Cachelagring i distribuerede programmer

Distribuerede programmer typisk implementerer et eller begge af følgende strategier når cachelagre data:

- Brug af en privat cache, hvor dataene opbevares lokalt på den computer, der kører en forekomst af et program eller en tjeneste.
- Brug af en delt cache, fungerer som en hyppig årsag, kan åbnes af flere processer og/eller maskiner.

I begge tilfælde kan cachelagring udføres klientsiden og/eller serversiden. Klientsiden cachelagring er udført af den proces, der indeholder brugergrænsefladen for et system, som en webbrowser eller på computeren.
Serversiden cachelagring er udført af den proces, der indeholder de business-tjenester, der kører fra en fjernplacering.

### <a name="private-caching"></a>Privat cachelagring

De mest grundlæggende typer cache er en i hukommelsen store. Det har opbevares i adresseområde af en enkelt proces og adgang til direkte via den kode, der kører i processen. Denne type cache er meget hurtigt at få adgang til. Det kan også angive et meget effektivt middel til lagring af mindre mængder statiske data, da størrelsen på en cache er typisk begrænset af mængde hukommelse, der er tilgængelige på den computer, der er vært for processen.

Hvis du har brug for til at cachelagre mere end det er fysisk muligt i hukommelsen, kan du skrive cachelagrede data til det lokale filsystem. Dette vil være langsommere at få adgang til end data, der findes i hukommelsen, men du skal stadig være hurtigere og mere pålidelige end hentning af data på tværs af et netværk.

Hvis du har flere forekomster af et program, der bruger denne model, der kører samtidig, har hver forekomst af programmet sin egen uafhængige cache holder sin egen kopi af dataene.

Tænk på en cache som et øjebliksbillede af de oprindelige data på et tidspunkt i fortiden. Hvis disse data ikke er statisk, er det sandsynligvis, andet programforekomster holde forskellige versioner af dataene i deres cachelagre. Derfor kan den samme forespørgsel, der er udført af disse tilfælde returnere andre resultater, som vist i figur 1.

![Ved hjælp af en datacache i hukommelsen i forskellige forekomster af et program](media/best-practices-caching/Figure1.png)

_Figur 1: Brug af en datacache i hukommelsen i forskellige forekomster af et program_

### <a name="shared-caching"></a>Delt cachelagring

Ved hjælp af en delt cache kan hjælpe med til at løse problemer, data kan variere i hver cache, som kan opstå i forbindelse med i hukommelsen cachelagring. Delte cachelagring sikrer, at andet programforekomster ser den samme visning af cachelagrede data. Det gør dette ved at finde cachen i en separat placering, typisk hostet som en del af en separat tjeneste, som vist i figur 2.

![Ved hjælp af en delt cache](media/best-practices-caching/Figure2.png)

_Figur 2: Brug af en delt cache_

En vigtig fordel af den delte cachelagring tilgang er den skalerbarhed, den indeholder. Mange delt cache tjenester er implementeret ved hjælp af en klynge af servere og bruge software, der distribuerer data på tværs af klyngen i en gennemsigtig måde. En forekomst af programmet sender blot en anmodning til tjenesten cache.
Den underliggende infrastruktur er ansvarlig for placering af de cachelagrede data i klyngen. Du kan nemt skalere cachen ved at tilføje flere servere.

Der er to primære ulemper ved den delte cachelagring tilgang:
- Cachen er langsommere få adgang til fordi den ikke længere er havde lokalt til hver forekomst af programmet.
- Kravet om at implementere en separat cachetjeneste kan føje kompleksitet til løsningen.

## <a name="considerations-for-using-caching"></a>Overvejelser ved brug af cachelagring

I følgende afsnit beskrives mere detaljeret overvejelser ved design og brug af en cache.

### <a name="decide-when-to-cache-data"></a>Afgøre, hvornår til at cachelagre data

Cachelagring kan markant forbedre ydeevnen, skalerbarhed og tilgængelighed. Jo flere data, du har og jo større antallet af brugere, der skal have adgang til disse data, der er større fordelene ved at blive-cachelager. Det skyldes, at cachelagring reducerer ventetid og konflikt, der er knyttet til håndtering af store mængder samtidige anmodninger i det oprindelige datalager.

En database understøtter muligvis et begrænset antal samtidige forbindelser. Henter data fra en delt cache, men gør i stedet for den underliggende database det muligt for en klientprogrammet og få adgang til disse data, selv hvis antallet af tilgængelige forbindelser i øjeblikket er opbrugt. Hvis databasen er tilgængelig, kan klientprogrammer desuden ikke kunne fortsætte ved hjælp af de data, der findes i cachen.

Overvej at cachelagre data, der er læse ofte, men ændret sjældent (for eksempel, data, der indeholder en højere del af Læs handlinger for at skrive handlinger). Dog anbefales ikke, at du bruger cachen som autoritative store af vigtige oplysninger. I stedet sikre, at alle ændringer, som dit program ikke vil risikere at miste gemmes altid i en fast datalager. Det betyder, at hvis cachen ikke er tilgængelig, dit program stadig kan fortsætte med at fungere ved hjælp af datalageret, og du ikke mister vigtige oplysninger.

### <a name="determine-how-to-cache-data-effectively"></a>Finde ud af, hvordan du cache data effektivt

Nøglen til effektiv brug af en cache ligger i bestemmelse af de mest relevante data til cache og cachelagring på det rette tidspunkt. Dataene, der kan føjes til cachen efter behov første gang det er hentet fra et program. Det betyder, at programmet skal til at hente dataene kun én gang fra datalager, og den efterfølgende adgang kan være opfyldt ved hjælp af cachen.

Du kan også kan en cache helt eller delvist udfyldes med data i forvejen, typisk når programmet starter (en metode kendt som forhåndsudfyldning). Det kan dog ikke hensigtsmæssigt at implementere forhåndsudfyldning til en stor cache, da denne metode kan angive en pludselig, høj belastning på det oprindelige datalager, når programmet starter.

En analyse af brugsmønstre kan ofte hjælpe dig med at beslutte, om at helt eller delvist på forhånd udfylde en cache og for at vælge, hvilke data til cache. Det kan for eksempel være nyttigt til forhåndsudfyldning cachen med profil statisk brugerdata for kunder, der bruger programmet jævnligt (måske hver dag), men ikke for kunder, der bruger programmet kun en gang om ugen.

Cachelagring typisk fungerer fint med data, der er fast eller, der sjældent ændres. Eksempler kan nævnes referenceoplysninger som produkt og prisoplysninger i en e-handel programmet, eller delte statisk ressourcer, der er dyrt at opbygge. Nogle eller alle disse data kan indlæses i cache ved start af programmet til at minimere demand på ressourcer og forbedre ydeevnen. Det kan også være relevante for en baggrundsproces, der jævnligt opdaterer reference data i cachen for at sikre, at det er opdateret, eller, der opdaterer cachen når refererer til data ændringer.

Cachelagring er mindre nyttige til dynamiske data, selvom der er nogle undtagelser til denne overvejelser (se afsnittet Cache meget dynamiske data senere i denne artikel for at få mere at vide). Når de oprindelige data ændres regelmæssigt, enten cachelagrede oplysninger bliver forældede meget hurtigt, eller spild af synkronisering cachen med det oprindelige datalager formindsker effektiviteten i forbindelse med cachelagring.

Bemærk, at en cache ikke behøver at være fuldført data for et objekt. Eksempelvis hvis et dataelement repræsenterer et med flere værdier objekt som en bankkunde med et navn, adresse og saldo, nogle af disse elementer stadig være statisk (såsom navn og adresse), mens andre (såsom saldoen) kan være mere dynamisk. I disse situationer, kan det være praktiske til lagring af de statiske dele af dataene og hente (eller beregne) kun de resterende oplysninger, når det er nødvendigt.

Vi anbefaler, at du kan udføre ydeevne test for og brugen analyser til at afgøre, om foreløbig populationen eller efter behov indlæsning af cachen eller en kombination af begge er relevant. Beslutningen skal baseres på flygtighed og mønster for brug af dataene. Cache anvendelsen og ydeevne analyse er særligt vigtig i programmer, der støder på stor belastning og skal være meget SVG. For eksempel i meget SVG scenarier det muligvis mening at podes cachen for at reducere belastningen på datalager på spidsbelastning.

Cachelagring kan også bruges til at undgå gentagne beregninger, mens programmet kører. Hvis handlingen transformerer data eller udfører en komplicerede beregning, kan det gemme resultaterne af handlingen i cachen. Hvis den samme beregning senere er påkrævet, kan programmet blot hente resultaterne fra cachen.

Et program kan redigere data, der findes i en cache. Vi anbefaler dog tænker på cachen som en midlertidig datalager, der kan forsvinder, når som helst. Gem ikke værdifulde data i cachen. Sørg for, at du opretholder oplysningerne i det oprindelige datalager samt. Det betyder, at hvis cachen er tilgængelig, du minimere risikoen for at miste data.

### <a name="cache-highly-dynamic-data"></a>Cache meget dynamiske data

Når du gemmer hurtigt ændre oplysninger i et fast datalager, kan det angive en omkostninger på systemet. For eksempel bør du overveje en enhed, der kontinuerligt rapporter status eller nogle andre mål. Hvis et program vælger ikke at cachelagre disse data på grundlag af, at de cachelagrede oplysninger næsten altid være forældet, kan de samme overvejelser være sandt, når lagring og hentning af disse oplysninger fra datalager. I den tid, det tager at gemme og hente disse data, kan det have ændret.

Overvej fordelene ved at gemme de dynamiske oplysninger direkte i cachen i stedet for i lageret med fast data i en situation som dette. Hvis dataene er ikke-kritisk og kræver ikke overvågning, derefter noget det ikke, hvis lejlighedsvise ændringen går tabt.

### <a name="manage-data-expiration-in-a-cache"></a>Administrere udløb af data i en cache

I de fleste tilfælde er data, der findes i en cache en kopi af data, der findes i det oprindelige datalager. Dataene i det oprindelige datalager kan ændres, når den blev cachelagret, medfører, at de cachelagrede data bliver forældede. Mange cachelagring systemer gør det muligt at konfigurere cachen for at udløbe data og reducere den periode, for hvilke data kan være forældet.

Når cachelagrede data udløber, er den fjernet fra cachen, og programmet skal hente data fra det oprindelige datalager (det kan sætte de nyligt hentet oplysninger tilbage i cachen). Du kan angive en udløbspolitik for en standard, når du konfigurerer cachen. I mange cache-tjenester, kan du også bestemme udløbsperiode for individuelle objekter, når du gemmer dem fra et program i cachen.
Nogle cache gør det muligt at angive udløbsperioden, som en absolut værdi eller et glidende værdi, der medfører, at elementet skal fjernes fra cachen, hvis det ikke er adgang til inden for den angivne tidsrum. Denne indstilling tilsidesætter Udløbspolitikken en hvilken som helst hele cachen, men kun for de angivne objekter.

> [AZURE.NOTE] Overvej udløbsperiode for cachen og de objekter, den indeholder omhyggeligt. Hvis du laver for kort, objekter udløber for hurtigt, og du vil reducere fordelene ved at bruge cachen. Hvis du har foretaget den periode, der er for lang tid, risikerer du de data, blive forældede.

Det er også muligt, at cachen kan udfylde, hvis data har tilladelse til at kunne bosiddende i lang tid. I dette tilfælde skal kan alle anmodninger om at tilføje nye elementer i cachen medføre nogle elementer, der skal fjernes gennemtvinge i en proces, kaldet eviction. Cache services fjerne typisk data på grundlag mindste senest anvendte (LRU), men du kan normalt tilsidesætte denne politik og forhindre, at elementer der udskilt. Hvis du indføre denne metode, risikerer du imidlertid overstiger den hukommelse, der er tilgængelig i cachen. Et program, der forsøger at tilføje et element til cachen mislykkes med en undtagelse.

Nogle cachelagring installationer kan indeholde flere eviction politikker. Der er flere typer eviction politikker. Dette omfatter:
- En senest anvendte politik (i den forventningen, at dataene, der ikke kræves igen).
- En første-i-første-out politik (ældste data er udskilt først).
- En politik for eksplicitte fjernelse af baseret på en udløst hændelse (såsom de data, der bliver ændret).

### <a name="invalidate-data-in-a-client-side-cache"></a>Bliver ugyldige data i en cache på klientsiden

Data, der findes i en klientsiden cache er generelt anses for at være uden for tilsyn af den tjeneste, som indeholder dataene til klienten. En tjeneste kan ikke direkte tvinge en klient til at tilføje eller fjerne oplysninger fra en klientsiden cache.

Det betyder, at det er muligt for en klient, der bruger en dårligt konfigurerede cache til at fortsætte med at bruge forældede oplysninger. Eksempelvis hvis udløb politikkerne for cachen ikke korrekt implementeret, kan en klient bruge forældede oplysninger, der er cachelagret lokalt, når oplysningerne i den oprindelige datakilde er blevet ændret.

Hvis du bygger et webprogram, der fungerer data over en HTTP-forbindelse, kan du implicit fremtvinge en webklient (såsom en browser eller webtjenesteproxy) til at hente de seneste oplysninger. Du kan gøre dette, hvis en ressource er opdateret ved en ændring i URI for ressourcen. Bruge Webklienter typisk URI for en ressource som nøgle i cachen klientsiden, så hvis URI ændres, webklienten ignorerer eventuelle tidligere cachelagret versioner af en ressource og henter den nye version i stedet.

## <a name="managing-concurrency-in-a-cache"></a>Administrere på dokumentsammenfald i en cache

Cachelagre er ofte designet skal deles med flere forekomster af et program. Hver forekomst af programmet kan læse og redigere data i cachen. Derfor gælder de samme på dokumentsammenfald problemer, der opstår med en hvilken som helst delt datalager også for en cache. En situation, hvor et program skal til at ændre data, der findes i cachen, kan du nødt til at sikre, at opdateringer fra én forekomst af programmet ikke overskriver de ændringer, der er foretaget af en anden forekomst.

Afhængigt af, hvilke data og sandsynligheden for kollisioner, kan du tage en af to måder at på dokumentsammenfald:

- __Optimistisk.__ Umiddelbart før du opdaterer dataene, applikationen kontrollerer, for at se, om data i cachen er ændret siden den blev hentet. Hvis dataene er stadig på samme måde, der er foretaget ændringen. Programmet, ellers har beslutte, om at opdatere. (Den forretningslogik, der styrer denne beslutning bliver program-specifikke). Denne metode er egnet til situationer, hvor opdateringer er sjælden, eller hvor kollisioner er sandsynlighed for.
- __Pessimistisk.__ Når det henter dataene, lukkes programmet i cachen til at forhindre en anden forekomst i at ændre den. Denne proces sikrer, at kollisioner kan forekomme, men de kan også blokere andre forekomster, der har brug for at behandle de samme data. Pessimistisk på dokumentsammenfald kan påvirke skalerbarhed af en løsning og kan kun anbefales for forbigående handlinger. Denne metode kan være relevante for situationer, hvor kollisioner er større sandsynlighed, især hvis et program opdaterer flere elementer i cachen og skal sikre, at ændringerne anvendes konsekvent.

### <a name="implement-high-availability-and-scalability-and-improve-performance"></a>Implementere høj tilgængelighed og skalerbarhed og forbedre ydeevnen

Undgå at bruge en cache som den primære lager af data. Dette er det oprindelige datalager, hvorfra cachen udfyldes rolle. Det oprindelige datalager er ansvarlig for at sikre brugerdata data.

Pas på ikke at introducere kritiske afhængigheder af tilgængeligheden af en delt cache-tjeneste til dine løsninger. Et program skal kunne fortsat fungerer, hvis den tjeneste, som indeholder delte cachen ikke er tilgængelig. Programmet hænger ikke eller ikke, mens du venter cachetjeneste til at fortsætte.

Derfor skal være forberedt programmet til at registrere tilgængeligheden af cachetjeneste og gå tilbage til det oprindelige datalager, hvis cachen er ikke tilgængelig. [Afbryder mønster](http://msdn.microsoft.com/library/dn589784.aspx) er nyttigt til håndtering af dette scenario. Den tjeneste, som indeholder cachen kan gendannes, og når det bliver tilgængelige, cachen kan tilføjes som data er skrivebeskyttet formular de oprindelige data store ved at følge en strategi som [et mønster Cache-side](http://msdn.microsoft.com/library/dn589799.aspx).

Men der kan være en skalerbarhed indvirkning på systemet Hvis programmet går tilbage til det oprindelige datalager, når cachen er ikke tilgængelig i øjeblikket.
Mens datalager gendannes, det oprindelige datalager kunne være oversvømmet med anmodninger om data, hvilket resulterer i timeout og mislykkedes forbindelser.

Overvej at implementere en lokal, private cache i hver forekomst af et program, sammen med den delte cache, som alle forekomster af tjenesteprogrammer adgang til. Når programmet henter et element, det kan du kontrollere først i den lokale cache, og klik derefter på delt cachen, og gemme til sidst i de oprindelige data. Den lokale cache kan udfyldes med dataene i den delte cache eller i databasen, hvis den delte cache er ikke tilgængelig.

Denne fremgangsmåde kræver forsigtig konfiguration for at forhindre, at den lokale cache blive for forældede i forhold til den delte cache. Den lokale cache fungerer dog som en bufferen, hvis den delte cache er ikke tilgængelig. Figur 3 viser denne struktur.

![Brug af en lokal, private cache med en delt cache](media/best-practices-caching/Caching3.png)
_figur 3: Brug af en lokal, private cache med en delt cache_

For at understøtte store cache, der indeholder data, relativ lang levetid, indeholder nogle cache tjenester en indstilling for høj tilgængelighed, der implementerer automatisk failover, hvis cachen bliver utilgængeligt. Denne metode omfatter typisk replikering de cachelagrede data, der er gemt på en primær cache server til en sekundær cache-server, og Skift til den sekundære server, hvis den primære server mislykkes eller connectivity går tabt.

For at reducere den ventetid, der er knyttet til at skrive til flere destinationer, opstå gentagelse til den sekundære server asynkront, når der skrives data til cachen på den primære server. Denne fremgangsmåde giver mulighed for, at nogle cachelagrede oplysninger kan gå tabt i tilfælde af en fejl, men del af disse data skal være lille sammenlignet med den samlede størrelse af cachen.

Hvis en delt cache er stor, kan det være nyttige for partition de cachelagrede data på tværs af noder til at reducere risikoen med konflikt og forbedre skalerbarhed. Mange delt cache understøtter muligheden for at tilføje dynamisk (og fjerne) noder og genoprette data på tværs af partitioner. Denne metode kan omfatte klynge, hvori præsenteres samling af noder til klientprogrammer som en problemfri, enkelt cache. Internt, men er dataene fordelt mellem noder efter en foruddefineret fordelingsstrategi, der afbalancerer afkrydsningsfeltet Indlæs jævnt. [Leverandør vejledning datadokument](http://msdn.microsoft.com/library/dn589795.aspx) på Microsofts websted indeholder flere oplysninger om mulige leverandør strategier.

Klynge kan også øge tilgængeligheden af cachen. Hvis en node mislykkes, er resten af cachen stadig tilgængelig.
Klynge bruges ofte sammen med gentagelse og belastningsjustering. Hver node kan replikeres, og replikaen kan hurtigt komme online Hvis noden mislykkes.

Mange læse og skrivehandlinger kan medføre enkelt dataværdier eller objekter. Dog være nogle gange det nødvendigt at gemme eller hente store mængder data hurtigt.
For eksempel kunne forhåndsudfyldning en cache involverer skriver hundredvis eller tusindvis af elementer i cachen. Et program muligvis også til at hente et stort antal relaterede elementer fra cachen som en del af den samme anmodning.

Mange store cache giver batchen handlinger til disse formål. Dette gør det muligt for en klientprogrammet pakke op en stor mængde elementer til en enkelt anmodning og reducerer spild, der er knyttet til at udføre et stort antal små anmodninger.

## <a name="caching-and-eventual-consistency"></a>Cachelagring og eventuel konsistens

Forekomsten af det program, der udfylder cachen skal have adgang til de seneste og ensartede version af dataene til mønsteret cache-side til at arbejde. Dette kan ikke være på store og små bogstaver i et system, der implementerer eventuel konsistens (som et replikerede datalager).

Én forekomst af et program kan redigere et dataelement og ødelægge den cachelagrede version af elementet. En anden forekomst af programmet forsøger at læse dette element fra en cache, som forårsager en fundne forekomster, så den læser data fra datalager og tilføjer den i cachen. Hvis datalager ikke er fuldt synkroniseret med de andre replikaer, kan forekomsten af programmet læse og udfylde cachen med den gamle værdi.

Finde flere oplysninger om håndtering af dataenes konsistens, på siden [Data konsistens grundlæggende](http://msdn.microsoft.com/library/dn589800.aspx) på Microsofts websted.

### <a name="protect-cached-data"></a>Beskytte cachelagrede data

Du kan bruge, overveje, hvordan du beskytter de data, der findes i cachen mod uautoriseret adgang uanset tjenesten cache. Der er to primære problemstillinger:

- Beskyttelse af data i cachen
- Beskyttelse af data, som den flyder mellem cachen og det program, der bruger cachen

For at beskytte data i cachen, kan cachetjeneste implementere en godkendelsesmetoden, der kræver, at angive følgende programmer:
- Hvilke identiteter kan få adgang til data i cachen.
- Hvilke handlinger (læse og skrive), som disse identiteter har tilladelse til at udføre.

Hvis du vil reducere spild, der er knyttet til læse og skrive data, når en identitet har fået tildelt, skrive og/eller læseadgang til cachen, identitet kan bruge alle data i cachen.

Hvis du har brug at begrænse adgangen til undersæt af de cachelagrede data, kan du gøre et af følgende:

- Opdele cachen i partitioner (ved hjælp af forskellige cache servere), og kun give adgang til identiteter til de partitioner, de skal have lov til brug.
- Kryptere dataene i hvert undersæt ved hjælp af forskellige nøgler, og giv tasterne kryptering kun til identiteter, som skal have adgang til hvert undersæt. En klientprogrammet kan stadig kunne hente alle data i cachen, men det vil kun kunne dekryptere de data, der er de pågældende taster.

Du skal også beskytte dataene, som teksten flyder og cachen. For at gøre dette, afhænger de sikkerhedsfunktioner, der leveres af netværksinfrastruktur, klientprogrammer bruger til at oprette forbindelse til cachen. Hvis cachen implementeres ved hjælp af en på stedet server i samme organisation, der er vært klientprogrammerne, derefter isolation af selve netværket muligvis ikke kræver, at du til at udføre yderligere trin. Hvis cachen er placeret fra en fjernplacering og kræver en TCP- eller HTTP-forbindelse via et offentligt netværk (såsom internettet), kan du overveje at implementere SSL.

## <a name="considerations-for-implementing-caching-with-microsoft-azure"></a>Overvejelser i forbindelse med implementering af cachelagring med Microsoft Azure

Azure leverer Azure Redis cachen. Dette er en implementering af Åbn kilden Redis cache, der kører som en tjeneste i en Azure datacenter. Den udbyder cachelagring, der kan åbnes fra en hvilken som helst Azure-program, om programmet er implementeret som en skybaseret tjeneste, et websted eller i en Azure virtuelt. Cachelagre kan deles af klientprogrammer, der har den relevante hurtigtast.

Azure Redis Cache er en høj ydeevne cachelagring løsning, der leverer tilgængelighed, skalerbarhed og sikkerhed. Der køres typisk som en tjeneste udbrede en eller flere dedikeret maskiner. Den forsøger at gemme så mange oplysninger som muligt i hukommelse til at sikre hurtig adgang. Denne arkitektur er beregnet til at levere kort ventetid og høj overførselshastighed ved at reducere behovet for at udføre langsom i/o-handlinger.

 Azure Redis Cache er kompatibelt med mange af de forskellige API'er, der bruges af klientprogrammer. Hvis du har eksisterende programmer, der allerede bruger Azure Redis Cache kører lokalt, giver Azure Redis cachen en hurtig migreringsstien til cachelagring i skyen.

> [AZURE.NOTE] Azure indeholder også Cachetjeneste administreres. Denne tjeneste er baseret på Azure Service-strukturen Cache-program. Det gør det muligt at oprette en distribuerede cache, der kan deles af løst forbundne programmer. Cachen er hostet på høj ydeevne servere, der kører på en Azure datacenter.
Men denne indstilling anbefales ikke længere og leveres kun til at understøtte eksisterende programmer, der er udviklet til at bruge den. For alle nye udvikling, skal du i stedet bruge Azure Redis Cache.
>
> Desuden understøtter Azure i rolle cachelagring. Denne funktion gør det muligt at oprette en cache, der er specifikke for en skybaseret tjeneste.
Cachen er hostet af forekomster af en web eller arbejder rolle, og kan kun åbnes af roller, der fungerer som en del af den samme sky service installation enhed. (En installation enhed er sæt af rolle forekomster, der er implementeret som en skybaseret tjeneste til et bestemt område). Cachen er grupperet, og alle forekomster af rolle i den samme installation enhed, der er vært cachen bliver en del af den samme klynge i cachen. Men denne indstilling anbefales ikke længere og leveres kun til at understøtte eksisterende programmer, der er udviklet til at bruge den. For alle nye udvikling, skal du i stedet bruge Azure Redis Cache.
>
> Både Azure administrerede Cachetjeneste og Azure i rolle cachen ske i øjeblikket til pensionen på November 16 2016.
Det anbefales, at du overfører til Azure Redis cachen i forberedelse til denne pensionen. Du kan finde flere oplysninger, gå til siden   [Hvad er Azure Redis Cache tilbud, og hvilken størrelse skal jeg bruge?](redis-cache/cache-faq.md#what-redis-cache-offering-and-size-should-i-use) på Microsofts websted.


### <a name="features-of-redis"></a>Funktioner i Redis

 Redis er mere end en simpel cache-server. Den indeholder en distribueret i hukommelsen database med en omfattende kommandosæt, der understøtter mange almindelige scenarier. Disse er beskrevet senere i dette dokument i den sektion ved hjælp af Redis cachelagring. Dette afsnit indeholder en oversigt over nogle af de vigtigste funktioner, som indeholder et Redis.

### <a name="redis-as-an-in-memory-database"></a>Redis som en database i hukommelsen

Redis understøtter begge læse og skrive handlinger. I Redis, kan være beskyttet skriver fra systemfejl enten ved at blive gemt med jævne mellemrum, i en lokal snapshot-fil eller i en Tilføj kun logfil. Det er ikke tilfældet i mange cachelagre (som skal betragtes som forbigående data butikker).

 Alle skriver er asynkron og blokerer ikke klienter fra læse og skrive data. Når Redis starter kører, den læser data fra filen øjebliksbillede eller log og bruges til at oprette i hukommelsen cachen. Se [Redis brugerdata](http://redis.io/topics/persistence) på webstedet Redis kan finde flere oplysninger.

> [AZURE.NOTE] Redis garanterer ikke, at alle skriver, gemmes i tilfælde af opstår nedbrud, men i værste kan du miste kun et par sekunder værd af data. Husk, at en cache ikke er beregnet til at fungere som en autoritative datakilde, og det er ansvarlig for de programmer, ved hjælp af cachen til at sikre, at kritiske data er gemt til et passende datalager. Finde flere oplysninger, det [mønster, cache-side](http://msdn.microsoft.com/library/dn589799.aspx).

#### <a name="redis-data-types"></a>Redis-datatyper

Redis er en nøgle-værdi butik, hvor værdier kan indeholde typer af enkel eller kompleks datastrukturer som hashes, lister, og sæt. Det understøtter et sæt af atomisk handlinger på disse datatyper. Taster kan være permanent eller mærket med et begrænset time-to-live, hvorefter tasten og dens tilsvarende værdi automatisk fjernet fra cachen. Du kan finde flere oplysninger om Redis nøgler og værdier, gå til siden [en introduktion til Redis datatyper og indvinding](http://redis.io/topics/data-types-intro) på webstedet Redis.

#### <a name="redis-replication-and-clustering"></a>Redis replikering og klynge

Redis understøtter overordnet/underordnet gentagelse for at sikre, at tilgængelige og vedligeholde overførselshastighed. Skriv handlinger til en overordnet node Redis replikeres til en eller flere underordnede noder. Læsehandlinger kan løses ved hjælp af masteren eller en af underordnede.

I tilfælde af et netværk partition underordnede kan fortsætte med at fungere data og derefter transparent synkronisere med masteren når forbindelsen er blevet genoprettet. Yderligere oplysninger finder du besøge siden [Gentagelse](http://redis.io/topics/replication) på webstedet Redis.

Redis indeholder også klynge, som gør muligt at transparent partition data til shards på tværs af servere og sprede afkrydsningsfeltet Indlæs. Denne funktion forbedrer skalerbarhed, fordi nye Redis servere kan tilføjes og de data, repartitioned som størrelsen på cachen øges.

Desuden kan hver server i klyngen replikeres ved hjælp af overordnet/underordnet gentagelse. Dette sikrer tilgængelighed på tværs af hver node i klyngen. Du kan finde flere oplysninger om klynge og sharding Besøg [Redis klynge selvstudium side](http://redis.io/topics/cluster-tutorial) på webstedet Redis.

### <a name="redis-memory-use"></a>Redis hukommelsesforbrug

En Redis cache har en endelig størrelse, der afhænger af de ressourcer, der er tilgængelige på computeren, host. Når du konfigurerer en Redis server, kan du angive den maksimale mængde hukommelse, der kan bruges. Du kan også konfigurere en nøgle i en Redis cache skal have et udløbstidspunkt, hvorefter den automatisk fjernet fra cachen. Denne funktion kan forhindre cachen i hukommelsen udfyldning med gamle eller forældede data.

Som hukommelse fyldes op, kan Redis automatisk fjerne nøgler og deres værdier ved at følge et antal politikker. Standard er LRU (mindst senest anvendte), men du kan også vælge andre politikker som evicting taster tilfældigt eller deaktivere eviction helt (under hvilke, tilfælde forsøger at føje elementer til cache-fejl, hvis det er fuld). Siden [Ved hjælp af Redis som en LRU cache](http://redis.io/topics/lru-cache) indeholder flere oplysninger.

### <a name="redis-transactions-and-batches"></a>Redis transaktioner og navne

Redis gør det muligt for en klientprogrammet til at sende en række operationer, læse og skrive data i cachen som en atomisk transaktion. Alle kommandoer i posteringen er garanti for at køre sekventielt, og ingen kommandoer, der er udstedt af andre samtidige klienter vil vævet mellem dem.

Disse er dog ikke SAND transaktioner som en relationel database skal udføre dem. Behandling af transaktioner består af to faser – først er, når kommandoerne, der er i kø, og andet er når kommandoerne køres. Under kommandoen kø fase sendes de kommandoer, der udgør posteringen af klienten. Hvis en slags fejlen opstår på dette tidspunkt (som en syntaksfejl eller et forkert antal parametre) derefter Redis afviser at behandle hele posteringen og sletter den.

I fasen køre udfører Redis hver i kø kommando i rækkefølge. Hvis en kommando ikke i denne fase, Redis fortsætter med kommandoen næste i køen og annullere ikke virkningerne af kommandoer, der allerede er udført. Forenklet formularen transaktion hjælper med at vedligeholde ydeevne og undgå problemer med ydeevnen, der er forårsaget af samme ressourcer.

Redis implementerer en slags optimistisk låsning for at hjælpe i bevarer konsistens. Du kan finde detaljerede oplysninger om transaktioner og låse med Redis du besøge [transaktioner side](http://redis.io/topics/transactions) på webstedet Redis.

Redis understøtter også ej samling af anmodninger. Redis protokollen klienter bruger til at sende kommandoer til en Redis server gør det muligt for en klient til at sende en række handlinger som en del af den samme anmodning. Dette kan hjælpe med at reducere pakke fragmentering på netværket. Når batchen behandles, udføres hver kommando. Hvis nogen af disse kommandoer er forkert udformet, de vil afvises (som ikke sker med en transaktion), men de resterende kommandoer, der skal udføres. Der er også ingen garanti om den rækkefølge, hvori kommandoerne i batchen behandles.

### <a name="redis-security"></a>Redis sikkerhed

Redis fokuserer på giver hurtig adgang til data, og er udviklet til at køre i et pålideligt miljø, der kan åbnes af klienter, der er tillid til. Redis understøtter en begrænset sikkerhedsmodel, der er baseret på godkendelse af adgangskode. (Det er muligt at fjerne godkendelse helt, selvom det ikke anbefales dette).

Alle godkendte klienter dele den samme globale adgangskode og har adgang til de samme ressourcer. Hvis du har brug for mere omfattende logon-sikkerhed, skal du implementere din egen sikkerhedslag foran Redis serveren, og alle klient-anmodninger skal gå gennem dette flere lag. Redis skal ikke direkte adgang til upålidelige eller ikke-godkendte klienter.

Du kan begrænse adgangen til kommandoer, ved at deaktivere dem eller omdøbe dem (og ved at indsende kun privilegerede klienter med de nye navne).

Redis understøtter ikke direkte enhver form for kryptering af data, så alle kodning skal udføres af klientprogrammer. Desuden giver Redis ikke en hvilken som helst form for transportsikkerhed. Hvis du har brug at beskytte data, som teksten flyder på tværs af netværket, anbefaler vi implementere en SSL-proxy.

Gå til siden [Redis sikkerhed](http://redis.io/topics/security) på webstedet Redis kan finde flere oplysninger.

> [AZURE.NOTE] Azure Redis Cache indeholder sit eget sikkerhedslag, som klienter opretter forbindelse. De underliggende Redis servere vises ikke det offentlige netværk.

### <a name="using-the-azure-redis-cache"></a>Ved hjælp af Azure Redis cachen

Azure Redis cachen giver adgang til Redis servere, der kører på servere, der er hostet hos en Azure datacenter det fungerer som en facaden, der indeholder adgangskontrol og sikkerhed. Du kan klargøre en cache ved hjælp af portalen Azure Management. Portalen indeholder en række foruddefinerede konfigurationer, lige fra en 53GB cache kører som en dedikeret tjeneste, der understøtter SSL-kommunikation (for beskyttelse af personlige oplysninger) og overordnet/underordnet replikering med en SERVICENIVEAUAFTALE af 99,9% tilgængelighed, ned til 250 MB cache uden gentagelse (ingen garantier med tilgængelighed) kører på delt hardware.

Ved hjælp af portalen Azure Management, kan du også konfigurere politikken eviction af cachen og styre adgangen til cachen ved at tilføje brugere til de roller, der er til rådighed Ejer, bidragyder, læser. Disse roller definere de handlinger, der kan udføre medlemmer. For eksempel medlemmer af rollen ejeren har fuld kontrol over cachen (herunder sikkerhed) og dens indhold, medlemmer af rollen Bidragyder kan læse og skrive oplysninger i cachen og medlemmer af rollen Læser kan kun hente data fra cachen.

De fleste administrative opgaver udføres via Azure Management-portalen, og derfor mange af de administrative kommandoer, der er tilgængelige i standardversionen af Redis der ikke er tilgængelige, herunder også muligheden for at redigere konfigurationen fra et program, lukning server Redis konfigurere yderligere slaves eller gennemtvinge Gem data til disk.

Azure management-portalen indeholder en praktisk grafisk visning, der gør det muligt at overvåge ydeevnen for cachen. For eksempel kan du se antallet af forbindelser, der udføres, antallet af anmodninger udføres lydstyrken for læsning og skrivning, og antallet cache rammer kontra udgivelsescachen. Brug af disse oplysninger, du kan bestemme effektiviteten af cachen, og hvis behov skifter til en anden konfiguration eller ændrer politikken eviction. Desuden kan du oprette beskeder, som sender mails til en administrator, hvis et eller flere kritiske målepunkter falder uden for en forventede område. Eksempelvis hvis antallet i cachen mislykkedes overstiger en bestemt værdi i den seneste time, en administrator kan have besked om som cachen muligvis for lille eller data kan blive udskilles for hurtigt.

Du kan også overvåge CPU, hukommelse og brug af netværket for cachen.

Yderligere oplysninger og eksempler viser, hvordan du kan oprette og konfigurere en Azure Redis Cache, gå til siden [omgang omkring Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) på Azure bloggen.

## <a name="caching-session-state-and-html-output"></a>Cachelagring session stat og HTML-output

Hvis du bygge ASP.NET web-programmer, kører ved hjælp af Azure web roller, kan du gemme session tilstandsoplysninger og HTML-output i en Azure Redis Cache. Session tilstand-Provider til Azure Redis Cache gør det muligt at dele oplysninger om sessionen mellem forskellige forekomster af et ASP.NET-webprogram, og være meget nyttigt i web-farm situationer, hvor klient / server-forbindelse er ikke tilgængelig og cachelagring session data i hukommelsen ville være uhensigtsmæssig.

Ved hjælp af Session tilstand Provider med Azure Redis Cache giver adskillige fordele i forbindelse, herunder:

- Det kan dele sessionstilstand blandt et stort antal forekomster af et ASP.NET-webprogram, give forbedret skalerbarhed
- Det understøtter kontrolleret, samtidige adgang til de samme session tilstand data i flere læsere og en enkelt writer og
- Det kan bruge komprimering til at gemme hukommelse og forbedre netværksydeevne.

Gå til siden [ASP.NET Session tilstand-Provider til Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) på Microsofts websted for at få flere oplysninger.

> [AZURE.NOTE] Brug ikke provideren Session tilstand til Azure Redis Cache til ASP.NET-programmer, der kører uden for det Azure-miljø. Ventetid for at få adgang til fra cachen uden for Azure kan eliminere ydeevne fordelene ved at cachelagre data.

På samme måde, Output Cache-Provider til Azure Redis Cache gør det muligt at gemme de HTTP-svar, der genereres af en ASP.NET web application. Ved hjælp af Output Cache Provider med Azure Redis Cache kan forbedre svar tidspunkter for programmer, der gengiver kompleks HTML-outputtet. forekomster af tjenesteprogrammer generere lignende svar kan gøre brug af delte output fragmenter i cachen i stedet for at generere HTML-kode output forfra.  Gå til siden [ASP.NET Output Cache-Provider til Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) på Microsofts websted for at få flere oplysninger.

### <a name="azure-redis-cache"></a>Azure Redis cache

Azure Redis Cache giver adgang til Redis servere, der er hostet hos en Azure datacenter. Det fungerer som en facaden, der indeholder adgangskontrol og sikkerhed. Du kan klargøre en cache ved hjælp af portalen Azure.

Portalen indeholder en række foruddefinerede konfigurationer. Disse område fra en 53 GB cache kører som en dedikeret tjeneste, der understøtter SSL-kommunikation (for beskyttelse af personlige oplysninger) og overordnet/underordnet replikering med en SERVICENIVEAUAFTALE af 99,9% tilgængelighed, ned til en 25 0 MB cache uden gentagelse (ingen garantier med tilgængelighed) kører på delt hardware.

Ved hjælp af portalen Azure, kan du også konfigurere politikken eviction af cachen og styre adgangen til cachen ved at tilføje brugere til de roller, der er angivet.  Disse roller, der definerer de handlinger, der kan udføre medlemmer, omfatter ejer, bidragyder og læser. For eksempel medlemmer af rollen ejeren har fuld kontrol over cachen (herunder sikkerhed) og dens indhold, medlemmer af rollen Bidragyder kan læse og skrive oplysninger i cachen og medlemmer af rollen Læser kan kun hente data fra cachen.

De fleste administrative opgaver udføres via Azure-portalen. Derfor er mange af de administrative kommandoer, der er tilgængelige i standardversionen af Redis ikke tilgængelige, herunder også muligheden for at ændre konfigurationen fra et program, Redis serveren lukkes, konfigurere yderligere underordnede eller gennemtvinge gemme data på disken.

Azure-portalen indeholder en praktisk grafisk visning, der gør det muligt at overvåge ydeevnen for cachen. For eksempel kan du se antallet af forbindelser, der udføres, antallet af anmodninger om der er udført, lydstyrken for læsning og skrivning, og antallet i cachen kontra udgivelsescachen. Bruger disse oplysninger, kan du bestemme effektiviteten i forbindelse med cachen og hvis det er nødvendigt, skifte til en anden konfiguration eller ændre politikken eviction.

Desuden kan du oprette beskeder, som sender mails til en administrator, hvis et eller flere kritiske målepunkter falder uden for en forventede område. Du kan for eksempel vil advare administrator, hvis antallet i cachen mislykkedes overstiger en bestemt værdi i den seneste time, fordi det betyder, at cachen være for små eller data kan blive udskilles for hurtigt.

Du kan også overvåge CPU, hukommelse og brug af netværket for cachen.

Yderligere oplysninger og eksempler viser, hvordan du kan oprette og konfigurere en Azure Redis Cache, gå til siden [omgang omkring Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) på Azure bloggen.

## <a name="caching-session-state-and-html-output"></a>Cachelagring session stat og HTML-output

Hvis du opretter fremgå ASP.NET web-programmer, kører ved hjælp af Azure web roller, kan du gemme session oplysninger og HTML-output i en Azure Redis Cache. Session tilstand-provider til Azure Redis Cache gør det muligt at dele oplysninger om sessionen mellem forskellige forekomster af et ASP.NET-webprogram, og være meget nyttigt i web-farm situationer, hvor klient / server-forbindelse er ikke tilgængelig og cachelagring session data i hukommelsen ville være uhensigtsmæssig.

Ved hjælp af session tilstand provider med Azure Redis Cache giver adskillige fordele i forbindelse, herunder:

- Dele sessionstilstand med et stort antal forekomster af ASP.NET web-programmer.
- Give forbedret skalerbarhed.
- Supplerende kontrolleret, samtidige adgang til de samme session tilstand data for flere læsere og en enkelt writer.
- Ved hjælp af komprimering til at gemme hukommelse og forbedre netværksydeevne.

Du kan finde flere oplysninger, gå til siden [ASP.NET session tilstand-provider til Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) på Microsofts websted.

> [AZURE.NOTE] Brug ikke provideren session tilstand til Azure Redis Cache med ASP.NET-programmer, der kører uden for det Azure-miljø. Ventetid for at få adgang til fra cachen uden for Azure kan eliminere ydeevne fordelene ved at cachelagre data.

På samme måde, output cache-provider til Azure Redis Cache gør det muligt at gemme de HTTP-svar, der genereres af en ASP.NET web application. Ved hjælp af output cache provider med Azure Redis Cache kan forbedre svar tidspunkter for programmer, der gengiver kompleks HTML-output. Forekomster af tjenesteprogrammer, generere lignende svar kan gøre brug af delte output fragmenter i cachen i stedet for at generere HTML-kode output forfra. Du kan finde flere oplysninger, gå til siden [ASP.NET output cache-provider til Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) på Microsofts websted.

## <a name="building-a-custom-redis-cache"></a>Opbygning af en brugerdefineret Redis cache

Azure Redis Cache fungerer som en facaden til de underliggende Redis-servere. I øjeblikket den understøtter et fast antal konfigurationer, men indeholder ikke til Redis klynge. Hvis du har brug for en avanceret konfiguration, ikke er omfattet af cachen Azure Redis (såsom en cache, der er større end 53 GB) kan du oprette og hoste dine egne Redis servere ved hjælp af Azure virtuelle maskiner.

Dette er en potentielt kompleks proces, fordi det være nødvendigt at oprette flere FOS skal fungere som overordnede og underordnede hierarkinoder, hvis du vil implementere gentagelse. Desuden, hvis du vil oprette en klynge, skal du flere mastere og underordnede servere. En topologi for minimale grupperet replikering, der indeholder en høj grad af tilgængelighed og skalerbarhed omfatter mindst seks FOS organiseret som tre par af overordnet/underordnet-servere (en klynge skal indeholde mindst tre overordnede noder).

Hvert overordnet/underordnet-par skal være placeret tæt på hinanden for at minimere ventetid. Hvert sæt af par kan dog være åbent i forskellige Azure datacentre, der er placeret i forskellige områder, hvis du vil finde cachelagrede data tæt på de programmer, der er mest sandsynlige det skal bruges. Siden, der [Kører Redis på en CentOS Linux VM i Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) på Microsofts websted vejleder gennem et eksempel, der viser, hvordan du kan oprette og konfigurere en Redis node kører som en Azure VM.

[AZURE.NOTE] Vær opmærksom på, hvis du implementerer din egen Redis cache på denne måde, du er ansvarlig for overvågning, administration og sikring af tjenesten.

## <a name="partitioning-a-redis-cache"></a>Partitionering en Redis cache

Partitionering cachen omfatter opdele cachen på tværs af flere computere. Denne struktur giver adskillige fordele i ved hjælp af en enkelt cache-server, herunder:

- Oprette en cache, der er meget større end kan være gemt på en enkelt server.
- Distribuere data på tværs af servere, forbedrer tilgængeligheden. Hvis en server, mislykkes eller bliver utilgængelige, de data, den indeholder ikke er tilgængelig, men dataene på de resterende servere kan stadig få adgang til. Til en cache er dette ikke afgørende fordi de cachelagrede data er kun en midlertidig kopi af de data, der findes i en database. Cachelagrede data på en server, der bliver utilgængelige kan cachelagres på en anden server i stedet.
- Sprede belastning på tværs af servere, således at forbedre ydeevne og skalerbarhed.
- Geolocating data tæt på de brugere, få adgang til det, hvilket reducerer ventetid.

For en cache er den mest almindelige form af partitionering sharding. I denne strategi er hver partition (eller shard) en Redis cache i sig selv. Data er ført til en bestemt partition ved hjælp af sharding logik, som kan bruge en række metoder til at distribuere dataene. [Sharding mønster](http://msdn.microsoft.com/library/dn589797.aspx) indeholder flere oplysninger om implementering af sharding.

For at implementere partitionering i en Redis cache, kan du udføre én af følgende fremgangsmåder:

- _Routing-serversiden forespørgsel._ I denne teknik sender en klientprogrammet en forespørgsel til en af de Redis servere, der udgør cachen (sandsynligvis den nærmeste server). Hver Redis server gemmer metadata, der beskriver den partition, den indeholder, og indeholder også oplysninger om, hvilke partitioner er placeret på andre servere. Redis serveren undersøger klientens anmodning. Hvis det kan løses lokalt, udfører den ønskede handling. Ellers videresender den anmodningen på den relevante server. Denne model er implementeret af Redis klynge og er beskrevet mere detaljeret på siden [Redis klynge selvstudium](http://redis.io/topics/cluster-tutorial) på webstedet Redis. Redis klynge er gennemsigtig til klientprogrammer, og yderligere Redis servere kan føjes til klyngen (og de data, der er igen opdelt) uden at kræve, at du omkonfigurere klienterne.

- _Klientsiden partitionering._ I denne model, klientprogrammet indeholder logik (muligvis i form af et bibliotek), som omdirigerer anmodninger til den relevante Redis. Denne metode kan bruges med Azure Redis Cache. Oprette flere Azure Redis cache (én for hver datapartition) og implementere klientsiden logik, som omdirigerer anmodningerne til den korrekte cache. Hvis det partitionsskema ændres (Hvis yderligere Azure Redis cache er oprettet, for eksempel), klientprogrammer muligvis konfigureres igen.

- _Proxy assisteret-partitionering._ I dette skema Redis klient programmer send forespørgsler til en mellemliggende proxytjenesten som forstår, hvordan dataene er opdelt og derefter dirigerer anmodningen til den rigtige server. Denne metode kan også bruges sammen med Azure Redis cachen. proxytjenesten kan implementeres som en Azure skybaseret tjeneste. Denne fremgangsmåde kræver et ekstra niveau af kompleksitet at implementere tjenesten, og som kan tage længere tid til at udføre end ved hjælp af klientsiden partitionering.

Siden [partitionering: hvordan du kan opdele data mellem flere forekomster af Redis](http://redis.io/topics/partitioning) på Redis websted indeholder yderligere oplysninger om implementering af partitionering med Redis.

### <a name="implement-redis-cache-client-applications"></a>Implementere Redis cache-klientprogrammer

Redis understøtter klientprogrammer, der er skrevet i flere programmeringssprog. Hvis du opretter nye programmer ved hjælp af .NET Framework, er den anbefalede måde at bruge biblioteket StackExchange.Redis klient. Dette bibliotek indeholder en .NET Framework-objektmodel, abstracts detaljerne for at oprette forbindelse til en Redis server, at sende kommandoer og modtage svar. Det er tilgængeligt i Visual Studio som en NuGet pakke. Du kan bruge denne samme bibliotek til at oprette forbindelse til en Azure Redis Cache eller en brugerdefineret Redis cache hostes på en VM.

Oprette forbindelse til en Redis server du bruger statiske `Connect` metode til at den `ConnectionMultiplexer` klasse. Den forbindelse, opretter denne metode er udviklet til at blive brugt i hele levetiden for klientprogrammet, og den samme forbindelse kan anvendes af flere samtidige tråde. Ikke genoprette forbindelse og Afbryd hver gang du udfører en handling af Redis, fordi dette kan påvirke ydeevnen.

Du kan angive forbindelsesparametrene som adressen på værten Redis og adgangskoden. Hvis du bruger Azure Redis Cache, er adgangskoden enten den primære eller sekundære nøgle, som genereres for Azure Redis Cache ved hjælp af portalen Azure Management.

Når du har forbindelse til serveren Redis, kan du få styr på Redis databasen, der fungerer som cachen. Redis tilslutning giver den `GetDatabase` metode til at gøre dette. Du kan derefter hente elementer fra cachen og gemme data i cachen ved hjælp af den `StringGet` og `StringSet` metoder. Disse metoder forventer en nøgle som en parameter, og returnere elementet, enten i cachen, der har en tilsvarende værdi (`StringGet`) eller tilføje elementet i cachen med denne tast (`StringSet`).

Afhængigt af placeringen af Redis-server, kan mange handlinger betale nogle ventetid, mens der sendes en anmodning til serveren og et svar, der returneres af klienten. Biblioteket StackExchange viser asynkron versioner af mange af de metoder, som den fremviser for at hjælpe klientprogrammer forbliver svarede. Disse metoder understøtter [Opgavebaseret asynkron mønster](http://msdn.microsoft.com/library/hh873175.aspx) i .NET Framework.

Følgende kodestykke viser en metode med navnet `RetrieveItem`. Den illustrerer en implementering af cache-side mønstret baseret på Redis og biblioteket StackExchange. Metoden tager en streng nøgleværdi og forsøger at hente det tilsvarende element fra cachen til Redis ved at ringe på `StringGetAsync` metode (den asynkrone version af `StringGet`).

Hvis elementet, der ikke findes, det er hentet fra de underliggende datakilde ved hjælp af den `GetItemFromDataSourceAsync` metode (som er en lokal metode og ikke en del af biblioteket StackExchange). Det derefter er føjet til cache ved hjælp af den `StringSetAsync` metode, så den kan hentes hurtigere næste gang.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Den `StringGet` og `StringSet` metoder er ikke begrænset til at hente eller gemme strengværdier. De kan tage et element, der er serialiseret som en matrix af byte. Hvis du vil gemme et .NET-objekt, du kan sekventielt som en byte-stream og bruge den `StringSet` metode til at skrive til cachen.

På samme måde, du kan læse et objekt fra cachen ved hjælp af den `StringGet` metode og deserialisering af det som en .NET-objekt. Følgende kode viser et sæt af lokalnummer metoder for grænsefladen IDatabase (den `GetDatabase` metode til en Redis forbindelse returnerer en `IDatabase` objekt), og nogle eksempelkode, der bruger disse metoder til at læse og skrive en `BlogPost` objekt til cachen:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Følgende kode illustrerer en metode med navnet `RetrieveBlogPost` , der bruger disse lokalnummer metoder til at læse og skrive en serialiserbar `BlogPost` objekt til cachen følge mønstret cache-side:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis understøtter kommandoen pipelining, hvis en klientprogrammet sender flere asynkrone anmodninger. Redis kan multiplex anmodninger ved hjælp af den samme forbindelse i stedet for at modtage og svare på kommandoer i en nøjagtig sekvens.

Denne fremgangsmåde hjælper med at reducere Ventetid ved at gøre mere effektiv brug af netværket. Følgende kodestykke viser et eksempel, der henter oplysninger om to kunder samtidig. Koden sender to anmodninger og derefter udfører nogle andre behandling af (ikke vist) før venter på at modtage resultaterne. Den `Wait` metode for objektet cache ligner .NET Framework `Task.Wait` metode:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

Siden [Azure Redis Cache dokumentation](https://azure.microsoft.com/documentation/services/cache/) på Microsofts websted indeholder flere oplysninger om, hvordan du kan skrive klientprogrammer, der kan bruge Azure Redis cachen. Yderligere oplysninger er tilgængelige på [siden grundlæggende brug af](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) på webstedet StackExchange.Redis.

Siden [rørledninger og multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) på det samme websted indeholder flere oplysninger om asynkrone handlinger og pipelining med Redis og biblioteket StackExchange.  I denne artikel, ved hjælp af Redis cachelagring, næste afsnit indeholder eksempler på nogle af de mere avancerede teknikker, du kan anvende på data, der findes i en Redis cache.

## <a name="using-redis-caching"></a>Brug af Redis cachelagring

Den nemmeste brugen af Redis for cachelagring af problemstillinger er nøgle-værdi-par, hvor værdien er en ufortolkede streng af vilkårlig længde, der indeholder binære data. (Det er grundlæggende en matrix af byte, der kan behandles som en streng). Dette scenarie blev vist i sektionen implementere Redis Cache klientprogrammerne tidligere i denne artikel.

Bemærk, at nøgler også indeholder ufortolkede data, så du kan bruge en hvilken som helst binære oplysninger som nøgle. Jo længere nøglen er dog mere plads det tager for at gemme og jo længere tid det tager for at udføre opslag handlinger. Designe din keyspace omhyggeligt til brugervenligheden og brugervenlighed vedligeholdelse, og bruge beskrivende (men ikke detaljeret) taster.

For eksempel bruge strukturerede taster som "kunde: 100" til at repræsentere tasten for kunden med ID 100 i stedet for blot "100". Denne metode kan du nemt at skelne mellem værdier, der gemmer forskellige datatyper. For eksempel kan du også bruge tasten "ordrer: 100" til at repræsentere nøglen for rækkefølge med ID 100.

Ud over en binær strenge, kan en værdi i et Redis nøgle-værdi-par også indeholde mere struktureret oplysninger, herunder lister, angiver (sorteret og usorteret) og udfører hashing. Redis giver et omfattende kommandosæt, der kan manipulere disse filtyper og mange af disse kommandoer findes i .NET Framework-programmer gennem en klient dokumentbibliotek som StackExchange. Siden [Introduktion til Redis datatyper og indvinding](http://redis.io/topics/data-types-intro) på webstedet Redis indeholder en mere detaljeret overblik over disse filtyper og de kommandoer, som du kan bruge til at ændre dem.

Dette afsnit indeholder en oversigt over nogle almindelige brug tilfælde for disse datatyper og kommandoer.

### <a name="perform-atomic-and-batch-operations"></a>Udføre atomisk og batch-handlinger

Redis understøtter en række atomisk get og set handlinger på strengværdier. Disse handlinger fjerne de mulige Kør risici, der kan opstå, når du bruger separat `GET` og `SET` kommandoer. De handlinger, der er tilgængelige, omfatter:

- `INCR`, `INCRBY`, `DECR`, og `DECRBY`, som udfører atomisk forøge eller formindske handlinger på heltal numeriske værdier. Biblioteket StackExchange viser overloadede versioner af den `IDatabase.StringIncrementAsync` og `IDatabase.StringDecrementAsync` metoder til at udføre disse handlinger og returnere den resulterende værdi, der er gemt i cachen. Følgende kodestykke viser, hvordan du bruger disse metoder:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET`, som henter den værdi, der er knyttet til en nøgle og ændres til en ny værdi. Biblioteket StackExchange gør denne handling tilgængelig via den `IDatabase.StringGetSetAsync` metode. Kodestykket nedenfor viser et eksempel på denne metode. Denne kode returnerer den aktuelle værdi, der er knyttet til tasten "data: tæller" fra det forrige eksempel. Det nulstiller derefter værdien for denne tast tilbage til nul, alle som en del af den samme handling:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET`og `MSET`, som kan returnere eller ændre et sæt af strengværdier som en enkelt handling. Den `IDatabase.StringGetAsync` og `IDatabase.StringSetAsync` metoder er overbelastede for at understøtte denne funktionalitet, som vist i følgende eksempel:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key-value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key-value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

Du kan også kombinere flere handlinger i en enkelt Redis transaktion, som beskrevet i afsnittet for Redis transaktioner og batches tidligere i denne artikel. Biblioteket StackExchange understøtter transaktioner via den `ITransaction` interface.

Du opretter en `ITransaction` objekt ved hjælp af den `IDatabase.CreateTransaction` metode. Du kalde kommandoer til posteringen ved hjælp af metoder, der leveres af den `ITransaction` objekt.

Den `ITransaction` interface giver adgang til en række metoder, der ligner dem, der åbnes af den `IDatabase` grænseflade, bortset fra at alle metoder er asynkron. Det betyder, at de er kun udføres, når den `ITransaction.Execute` metode startes. Den værdi, der returneres af den `ITransaction.Execute` metode angiver, om posteringen blev oprettet (SAND), eller hvis det lykkedes ikke (FALSK).

Følgende kodestykke viser et eksempel, intervaller og mindsker to tællere som en del af den samme transaktion:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Husk på, at Redis transaktioner i modsætning til transaktioner i relationsdatabaser. Den `Execute` metode kø blot alle de kommandoer, der udgør posteringen skal køres, og hvis enhver af dem er forkert udformet stoppes posteringen. Hvis alle kommandoer har sat i korrekt kø, kører hver kommando asynkront.

Hvis en kommando mislykkes, har de andre fortsætte behandling. Hvis du har brug at bekræfte, at en kommando er fuldført, skal du hente resultaterne af kommandoen ved hjælp af egenskaben **resultatet** af den tilsvarende opgave, som vist i eksemplet ovenfor. Læse egenskaben **resultatet** vil blokere den opkald tråd, indtil opgaven er afsluttet.

Finde yderligere oplysninger finder du på siden [transaktioner i Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) på webstedet StackExchange.Redis.

Når du udfører batchen handlinger, du kan bruge den `IBatch` grænseflade for biblioteket StackExchange. Brugergrænsefladen giver adgang til en række metoder forslag til søgeord af den `IDatabase` grænseflade, bortset fra at alle metoder er asynkron.

Du opretter en `IBatch` objekt ved hjælp af den `IDatabase.CreateBatch` metode og derefter køre batchen ved hjælp af den `IBatch.Execute` metode, som vist i følgende eksempel. Denne kode blot angiver en strengværdi, intervaller og mindsker de samme tællere, der bruges i det forrige eksempel, og resultaterne vises:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

Det er vigtigt at forstå, i modsætning til en transaktion, hvis en kommando i en batch mislykkes, fordi det er forkert udformet, de andre kommandoer kan stadig køre. Den `IBatch.Execute` metode returnerer ikke nogen angivelse af lykkes eller mislykkes.

### <a name="perform-fire-and-forget-cache-operations"></a>Udføre fire og glemmer cache handlinger

Redis understøtter fire og glemmer handlinger ved hjælp af kommandoen flag. I så fald klienten blot starter en handling, men har ingen interesse i resultatet og venter ikke på kommandoen til at være fuldført. Eksemplet herunder viser, hvordan du udfører kommandoen INCR som brand og glemmer handling:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### <a name="specify-automatically-expiring-keys"></a>Angive automatisk udløber taster

Når du gemmer et element i en Redis cache, kan du angive en timeout, hvorefter elementet bliver automatisk fjernet fra cachen. Du kan også forespørge, hvor meget mere tid har en nøgle, før det udløber ved hjælp af den `TTL` kommandoen. Denne kommando er tilgængelig til StackExchange programmer ved hjælp af den `IDatabase.KeyTimeToLive` metode.

Følgende kodestykke viser, hvordan til at angive en udløbsdato 20 sekunder på en tast, og forespørge resterende levetiden for tasten:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Du kan også angive udløbsdatoen til en bestemt dato og klokkeslæt ved hjælp af kommandoen UDLØBE, der findes i biblioteket StackExchange som den `KeyExpireAsync` metode:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Tip:_ Du kan fjerne et element fra cachen manuelt ved hjælp af kommandoen DEL, som fås via biblioteket StackExchange som den `IDatabase.KeyDeleteAsync` metode.

### <a name="use-tags-to-cross-correlate-cached-items"></a>Bruge mærker til at kryds-correlate cachelagrede elementer

Et Redis sæt er en samling af flere elementer, der deler en enkelt nøgle. Du kan oprette et sæt ved hjælp af kommandoen SADD. Du kan hente elementer i et sæt ved hjælp af kommandoen SMEMBERS. Biblioteket StackExchange implementerer kommandoen SADD med den `IDatabase.SetAddAsync` kommandoen metode og SMEMBERS med den `IDatabase.SetMembersAsync` metode.

Du kan også kombinere eksisterende sæt til at oprette nye sæt ved hjælp af SDIFF (sæt forskel), GLASFILTER (sæt skæringspunkt) og SUNION (sæt union) kommandoer. Biblioteket StackExchange samler disse handlinger i den `IDatabase.SetCombineAsync` metode. Den første parameter til denne metode angiver handlingen sæt til at udføre.

De følgende kodestykker viser, hvordan sæt kan være nyttige til hurtigt at gemme og hentning af samlinger af relaterede elementer. Denne kode bruger den `BlogPost` type, der er beskrevet i afsnittet implementere Redis Cache klientprogrammer tidligere i denne artikel.

A `BlogPost` objekt indeholder fire felter – et ID, en titel, et rangering resultat og en samling af mærker. Det første kodestykke nedenfor viser de eksempeldata, der bruges til at udfylde en C#-liste over `BlogPost` objekter:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags--assigned from a collection
                                  // in the tags list
}
```

Du kan gemme koderne for hver `BlogPost` objekt som et sæt i en Redis cache og knytte hvert sæt til ID'ET for den `BlogPost`. Dette gør det muligt for et program til hurtigt at finde alle de mærker, der hører til et bestemt blogindlæg. Du kan oprette et ekstra sæt, der indeholder det blogindlæg, henvises til mærke-ID på tasten for at aktivere søgning i den modsatte retning og finde alle blogindlæg, der deler et bestemt mærke:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in Redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Disse strukturer gør det muligt at udføre mange almindelige forespørgsler meget effektivt. For eksempel kan du finde og få vist alle mærkerne til blogindlæg 1 således:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Du kan finde alle mærker, der er fælles for blog udgive 1 og blog indlæg 2 ved at udføre en skæringspunkt handling, som følger:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Og du kan finde alle blogindlæg, der indeholder et bestemt mærke:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### <a name="find-recently-accessed-items"></a>Finde elementer, der er åbnet for nyligt

En almindelig opgave, der kræves af mange programmer er at finde mest elementer, der er åbnet for nyligt. For eksempel måske et blogge websted til at få vist oplysninger om senest læst blogindlæg.

Du kan implementere denne funktionalitet ved hjælp af en Redis liste. En Redis liste indeholder flere elementer, der deler den samme nøgle. Listen fungerer som en dobbelt afsluttet kø. Du kan skubbe elementer til enten slutningen af listen ved hjælp af LPUSH (venstre push) og RPUSH (højre push) kommandoer. Du kan hente elementer fra enten slutningen af listen ved hjælp af kommandoerne LPOP og RPOP. Du kan også returnere et sæt af elementer ved hjælp af kommandoerne LRANGE og RRANGER.

De kodestykker nedenfor viser, hvordan du kan udføre disse handlinger ved hjælp af biblioteket StackExchange. Denne kode bruger den `BlogPost` type fra de tidligere eksempler. Som et blogindlæg læses af en bruger på `IDatabase.ListLeftPushAsync` metode skubber titlen på blogindlægget på en liste, der er knyttet til tasten "blog:recent_posts" i Redis cachen.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // Reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // Push the blog post onto the list
```

Når flere blogindlæg læses, publiceres deres titler på den samme liste. På listen er sorteret efter den rækkefølge, hvori titlerne har tilføjet. Senest læst blogindlæg er mod venstre side af listen. (Hvis den samme blogindlæg skal skrives mere end én gang, har flere poster på listen.)

Du kan få vist titlerne på de senest læst indlæg ved hjælp af den `IDatabase.ListRange` metode. Denne metode tager nøglen, som indeholder listen, et udgangspunkt og et slutpunkt. Følgende kode henter titler for de 10 blogindlæg (elementer fra 0 til 9) i slutningen af listen længst til venstre:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Bemærk, at den `ListRangeAsync` metode ikke fjerne elementer på listen. For at gøre dette, kan du bruge den `IDatabase.ListLeftPopAsync` og `IDatabase.ListRightPopAsync` metoder.

Hvis du vil forhindre, at listen bliver på ubestemt tid, kan du med jævne mellemrum udsættersøer elementer ved at trimme på listen. Kodestykket nedenfor viser, hvordan du fjerner alle, men de fem længst til venstre elementer på listen:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### <a name="implement-a-leader-board"></a>Implementere en fyldtegn tavle

Elementer i et sæt afholdes som standard ikke i en bestemt rækkefølge. Du kan oprette et sorteret sæt ved hjælp af kommandoen ZADD (den `IDatabase.SortedSetAdd` metode i biblioteket StackExchange). Elementerne er sorteret på ved hjælp af en numerisk værdi, kaldet et resultat, som leveres som en parameter til kommandoen.

Følgende kodestykke føjer titlen på et blogindlæg til en sorteret liste. I dette eksempel har hver blogindlæg også et resultat felt, der indeholder rangeringsmodel i blogindlægget.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // Reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Du kan hente blog indlæg titler og pointtal i stigende rækkefølge resultat ved hjælp af den `IDatabase.SortedSetRangeByRankWithScores` metode:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE] Biblioteket StackExchange også giver den `IDatabase.SortedSetRangeByRankAsync` metode, der returnerer dataene i karakteren rækkefølge, men ikke returnerer resultaterne.

Du kan også hente elementer i faldende rækkefølge af pointtal, og Begræns antallet af elementer, der returneres ved at angive yderligere parametre til den `IDatabase.SortedSetRangeByRankWithScoresAsync` metode. Det næste eksempel viser titler og resultater af de øverste 10 rangerede blogindlæg:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

I næste eksempel bruges den `IDatabase.SortedSetRangeByScoreWithScoresAsync` metode, som du kan bruge til at begrænse de elementer, der skal returneres til dem, der falder inden for et givet antal point område:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### <a name="message-by-using-channels"></a>Meddelelse ved hjælp af kanaler

Ud over fungerer som en datacache, indeholder en Redis server messaging ved hjælp af en høj ydeevne publisher/abonnement ordning. Klientprogrammer kan abonnere på en kanal, og andre programmer eller tjenester kan udgive meddelelser til kanalen. Abonnere programmer modtager derefter disse meddelelser og kan behandle dem.

Redis indeholder kommandoen Abonner for klientprogrammer kan bruge til at abonnere på kanaler. Denne kommando forventer, at navnet på en eller flere kanaler, som programmet vil acceptere meddelelser. Biblioteket StackExchange indeholder den `ISubscription` -grænsefladen, der gør det muligt for en .NET Framework-program at abonnere og publicere kanaler.

Du opretter en `ISubscription` objekt ved hjælp af den `GetSubscriber` metode til at forbindelsen til Redis serveren. Derefter du lytte til meddelelser på en kanal ved hjælp af den `SubscribeAsync` metode til dette objekt. Følgende kodeeksempel viser, hvordan du abonnerer på en kanal, med navnet "meddelelser: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Den første parameter til den `Subscribe` metode er navnet på kanalen. Dette navn følger de samme regler, der bruges af taster i cachen. Navnet kan indeholde en hvilken som helst binære data, selvom det anbefales at bruge relativt kort og sigende strenge for at sikre god ydeevne og vedligeholde.

Bemærk også, at det navneområde, der anvendes af kanaler er adskilt fra, der bruges af taster. Det betyder, at du kan have kanaler og taster, der har det samme navn, selvom det kan gøre din programkode sværere at vedligeholde.

Den anden parameter er en handling stedfortræder. Denne stedfortræder kører asynkront, hver gang en ny meddelelse vises på kanalen. I dette eksempel vises blot meddelelse på konsollen (meddelelsen skal indeholde titlen på et blogindlæg).

Hvis du vil publicere til en kanal, kan et program bruge kommandoen Redis UDGIVELSE. Biblioteket StackExchange indeholder den `IServer.PublishAsync` metode til at udføre denne handling. Det næste kodestykke viser, hvordan du publicerer en meddelelse til kanalen, "meddelelser: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Der er flere punkter, skal du vide, om Udgiv/Abonner metode:

- Flere abonnenter kan abonnere på den samme kanal, og de får vist alle de meddelelser, der udgives på denne kanal.
- Abonnenter kun modtage meddelelser, der er blevet publiceret, når de har tilmeldt dig. Kanaler i bufferen ikke, og når meddelelsen er blevet publiceret, Redis infrastrukturen flytter meddelelsen til hver abonnement og derefter fjerne den.
- Som standard modtages meddelelser af abonnenter i den rækkefølge, hvori de sendes. I en meget aktive systemet med et stort antal meddelelser og mange abonnenter og udgivere, kan garantere sekventielle levering af meddelelser nedsætte systemets ydeevne. Hvis alle meddelelser, er uafhængig og rækkefølgen er ligegyldig, kan du aktivere samtidige behandling af Redis system, som kan hjælpe med at forbedre reaktionstid. Du kan opnå dette i en StackExchange klient ved at angive PreserveAsyncOrder af forbindelsen bruges af abonnement til falsk:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
redisHostConnection.PreserveAsyncOrder = false;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
```

## <a name="related-patterns-and-guidance"></a>Relaterede mønstre og vejledning

Følgende mønster kan også være relevant for det pågældende scenarie, når du implementerer cachelager i dine programmer:

- [Cache-side mønster](http://msdn.microsoft.com/library/dn589799.aspx): dette mønster beskriver, hvordan du indlæser data efter behov i en cache fra et datalager. Dette mønster også hjælper med at bevare konsistens mellem data, der findes i cachen og dataene i det oprindelige datalager.
- [Sharding mønster](http://msdn.microsoft.com/library/dn589797.aspx) indeholder oplysninger om implementering af vandret partitionering for at forbedre skalerbarhed ved lagring og få adgang til store datamængder.

## <a name="more-information"></a>Få mere at vide

- Siden [MemoryCache klasse](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) på Microsofts websted
- Siden [Azure Redis Cache dokumentation](https://azure.microsoft.com/documentation/services/cache/) på Microsofts websted
- Siden [Azure Redis Cache ofte stillede spørgsmål](redis-cache/cache-faq.md) på Microsofts websted
- Siden [konfiguration model](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) på Microsofts websted
- Siden [Opgavebaseret asynkron mønster](http://msdn.microsoft.com/library/hh873175.aspx) på Microsofts websted
- Siden [rørledninger og multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) på StackExchange.Redis GitHub repo
- Siden [Redis brugerdata](http://redis.io/topics/persistence) på webstedet Redis
- [Replikering side](http://redis.io/topics/replication) på webstedet Redis
- Siden [Redis klynge selvstudium](http://redis.io/topics/cluster-tutorial) på webstedet Redis
- Den [partitionering: hvordan du kan opdele data mellem flere forekomster af Redis](http://redis.io/topics/partitioning) side på webstedet Redis
- Siden [Ved hjælp af Redis som en LRU Cache](http://redis.io/topics/lru-cache) på webstedet Redis
- Siden [poster](http://redis.io/topics/transactions) på webstedet Redis
- Siden [Redis sikkerhed](http://redis.io/topics/security) på webstedet Redis
- Siden [omgang omkring Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) på Azure bloggen
- Siden [Kører Redis på en CentOS Linux VM i Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) på Microsofts websted
- Siden [ASP.NET session tilstand-provider til Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) på Microsofts websted
- Siden [ASP.NET output cache-provider til Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) på Microsofts websted
- [En introduktion til Redis datatyper og indvinding](http://redis.io/topics/data-types-intro) side på webstedet Redis
- Den [grundlæggende brug](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) side på webstedet StackExchange.Redis
- Siden [transaktioner i Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) på StackExchange.Redis repo
- [Data leverandør guide](http://msdn.microsoft.com/library/dn589795.aspx) på Microsofts websted
