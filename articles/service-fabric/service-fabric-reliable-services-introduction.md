<properties
   pageTitle="Oversigt over Service-strukturen pålidelig Service programming modellen | Microsoft Azure"
   description="Få mere at vide om tjenesten strukturen pålidelig Service programming model, og Kom i gang skrive dine egne tjenester."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# <a name="reliable-services-overview"></a>Oversigt over pålidelige Services
Azure Service-strukturen forenkler skrivning og administration af uden status og med høj sikkerhed pålidelig tjenester. Dette dokument kan tale om:

- Den pålidelige Services programming model for uden status og med høj sikkerhed.
- De valg, du skal gøre, når du skriver en pålidelig Service.
- Nogle scenarier og eksempler på, hvornår du skal bruge pålidelig tjenester, og hvordan de er skrevet.

Pålidelige Services er en af de tilgængelige på Service-strukturen programming modeller. Se [Introduktion til tjenesten strukturen pålidelig aktører](service-fabric-reliable-actors-introduction.md)kan finde flere oplysninger om pålidelig aktører programming modellen.

En tjeneste består af konfiguration, programkode, i tjenesten strukturen og (valgfrit) tilstand.

Tjenesten strukturen administrerer levetiden for tjenester fra klargøring og implementering via opgradering og sletning via [tjenesten strukturen programadministration](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Hvad er pålidelig tjenester?
Pålidelige tjenester giver dig en enkel, effektiv, på øverste niveau programming model kan hjælpe dig med express Hvad er det vigtigt at dit program. Med pålidelig tjenesterne programming model, får du:

- Pålidelige Services programming modellen kan for med høj sikkerhed tjenester, skal du konsekvent og sikkert at gemme dit tilstand direkte i din tjeneste ved hjælp af pålidelige websteder. Dette er et simpelt sæt meget tilgængelige Samlingsklasser, der er velkendt for alle, der er brugt C# af websteder. Traditionelt tjenester, der kræves eksterne systemer til administration af stabil tilstand. Du kan gemme din stat ud for din Beregn med pålidelig samlinger med den samme høj tilgængelighed og pålidelighed, du er kommet til forvente fra meget tilgængelige eksterne butikker og med flere ventetid forbedringer, som samtidig at finde Beregn og tilstand giver.

- En enkelt model for at køre din egen programkode, der ser ud som programming modeller, du har brugt. Din kode har en klart defineret indgangspunkt og nemt administreret levetid.

- En tilslutningsbar kommunikation model. Brug transport af dit valg, som HTTP med [Web API](service-fabric-reliable-services-communication-webapi.md), WebSockets, brugerdefinerede TCP-protokoller osv. Pålidelige Services indeholder nogle gode ud for de indstillinger, du kan bruge, eller du kan angive dine egne.

## <a name="what-makes-reliable-services-different"></a>Hvad gør pålidelig Services forskellige?
Pålidelige tjenester i tjenesten strukturen er forskellig fra tjenester, du har skrevet før. Tjenesten strukturen giver pålidelighed, tilgængelighed, konsistens og skalerbarhed.  

- **Pålidelighed**– din tjeneste forbliver dig selv i upålidelig miljøer, hvor dine maskiner kan mislykkes, eller tryk på netværksproblemer.

- **Tilgængelighed**– din tjeneste bliver webtabellerne og svarede. (Dette ikke betyder, at du ikke har tjenester, der blev ikke fundet eller nås fra uden for.)

- **Skalerbarhed**– Services er frakoblet fra bestemte hardware, og de kan Forøg eller Formindsk efter behov gennem tilføjelse eller fjernelse af hardware eller virtuelle ressourcer. Services er nemt opdelt (især i med høj sikkerhed tilfælde) for at sikre, at uafhængige dele af tjenesten kan skalere og reagere på fejl, uafhængigt af hinanden. Til sidst, opfordrer Service-strukturen tjenester for at kunne lette ved at tillade tusindvis af tjenester til at blive klargjort i en enkelt proces i stedet for at kræve eller dedikere hele OS forekomster til en enkelt forekomst af en bestemt arbejdsbyrde.

- **Konsistens**– nogle af de oplysninger, der er gemt i denne tjeneste kan sikres, at være konsekvente (gælder kun for med høj sikkerhed services - mere om dette senere)

## <a name="service-lifecycle"></a>Tjenesten livscyklus
Om din tjeneste er med høj sikkerhed eller uden tilstand, giver pålidelig Services en simpel livscyklus, hvor du kan hurtigt Tilslut din kode og komme i gang.  Der findes et eller to metoder, du skal bruge til at implementere for at få din tjeneste op at køre.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - dette er, hvor tjenesten definerer kommunikation stablen, det vil bruge. Kommunikation stablen, såsom [Web API](service-fabric-reliable-services-communication-webapi.md), er, hvad definerer lytte slutpunkt eller slutpunkter for tjenesten (hvordan klienter vil når derhen). Den definerer også hvordan de meddelelser, der vises ender interaktion med resten af koden tjeneste.

- **RunAsync** - dette er, hvor tjenesten kører dens forretningslogik. Annullering tokenet, der leveres er et signal for når, der fungerer skal stoppe. Hvis du har en tjeneste, der skal adskille meddelelser af en pålidelig kø og behandle dem konstant, er dette for eksempel hvor, der fungerer ville ske.

### <a name="service-startup"></a>Tjenesten blev startet

Vigtige begivenheder i en pålidelig Service livscyklus er:

1. Serviceobjektet (de ting, der er afledt fra uden status tjenesten eller med høj sikkerhed tjenesten) er opbygget.

2. Den `CreateServiceReplicaListeners` / `CreateServiceInstanceListeners` metode kaldes, hvor tjenesten mulighed for at returnere en eller flere kommunikation lyttefunktioner efter eget valg.
  - Bemærk, at dette er valgfrit, selvom de fleste tjenester fremvise direkte nogle slutpunkt.

3. Når kommunikation lyttere er oprettet, er det åbnes.
  - Kommunikation, har du en metode med navnet `OpenAsync()`, som kaldes på dette tidspunkt, og som returnerer lytte adressen til tjenesten. Hvis din pålidelig Service bruger en af de indbyggede ICommunicationListeners, findes der i stedet for dig.

4. Når kommunikation lytteren er åben, skal den `RunAsync()` metode på den overordnede tjeneste kaldes.
  - Bemærk, at `RunAsync()` er valgfrit. Hvis tjenesten ikke alle dens arbejde direkte i svar til bruger ringer kun, der er ingen grund til at implementere `RunAsync()`.

### <a name="service-shutdown"></a>Lukning af tjeneste

Når tjenesten lukkes (der skal slettes, opgraderet, eller er flyttet) opkald rækkefølgen vises spejlvendt: først annulleringen tokenet besiddelse af `RunAsync()` er annulleret. derefter `CloseAsync()` kaldes på kommunikation lyttere.

Der er nogle vigtige ting, du skal være opmærksom om lukning for med høj sikkerhed tjenester:

- Tjenesten strukturen bliver ikke hæve en anden kopi af din tjeneste til primære status indtil `CloseAsync` og `RunAsync` har returneres. Hvis du bruger en indbygget kommunikation lytter på `CloseAsync` metode håndteres for dig.

- Mens der er ingen tid grænse på returnere fra disse metoder, du mister straks muligheden for at skrive til pålidelig af websteder og derfor ikke kan gennemføre en hvilken som helst reelt tal arbejde. Det anbefales, at du vender tilbage så hurtigt som muligt ved modtagelse annulleringen anmodningen.

## <a name="example-services"></a>Eksempel på tjenester
Viden om denne programming model, Lad os kaste et hurtigt blik på to forskellige tjenester til at se, hvordan disse elementer passer sammen.

### <a name="stateless-reliable-services"></a>Uden status pålidelig tjenester
En uden status tjeneste er én hvor der er direkte ingen tilstand inden for tjenesten, eller den tilstand, som er til stede er helt disponible og kræver ikke synkronisering, gentagelse, brugerdata eller høj tilgængelighed.

For eksempel bør du overveje en Lommeregner, der har ingen hukommelse og modtager alle betingelser og handlinger til at udføre på én gang.

I dette tilfælde kan RunAsync() for tjenesten være tom, fordi der ikke er ingen baggrund opgave-behandling, tjenesten skal udføre. Når tjenesten Lommeregner oprettes, returneres en ICommunicationListener (for eksempel [Web API](service-fabric-reliable-services-communication-webapi.md)), der åbnes et lytte slutpunkt på nogle port. Dette lytte slutpunkt kan sætte til de forskellige metoder (eksempel: "Tilføj (n1, n2)"), der definerer den Lommeregner offentlige API.

Når et opkald foretages fra en klient, vil den relevante metode startes og tjenesten Lommeregner udfører handlinger på oplysningerne og returnerer resultatet. Den indeholder ikke en hvilken som helst tilstand.

Dette eksempel Lommeregner gør ikke at gemme en hvilken som helst interne tilstand meget simpel. Men de fleste tjenester er ikke helt igennem uden tilstand. I stedet for externalize de deres tilstand til nogle andre store. (For eksempel en hvilken som helst web app, der er afhænger af bevarelse sessionstilstand i en sikkerhedskopiering butik eller cachen er ikke helt uden tilstand).

Et almindeligt eksempel på hvordan uden status services bruges i tjenesten struktur er som en front-end, der viser offentligt API til et webprogram. Tjenesten front end taler derefter til med høj sikkerhed services til at udføre en anmodning om bruger. I dette tilfælde opkald fra klienter, dirigeres til en kendte port, som 80, hvor tjenesten uden status lytter. Uden status tjenesten modtager opkaldet og bestemmer, om opkaldet er fra en pålidelig part, samt hvilke tjenesten er det beregnet til.  Derefter tjenesten uden status videresender opkaldet til den korrekte partition af tjenesten med høj sikkerhed og venter på svar. Når tjenesten uden status modtager et svar, svar det tilbage til den oprindelige klient.

### <a name="stateful-reliable-services"></a>Med høj sikkerhed pålidelig tjenester
En med høj sikkerhed tjeneste er en, der skal have en del af tilstand bevares ensartede og Præsenter i rækkefølge for tjenesten til funktionen. Overvej en tjeneste, der beregner en rullende gennemsnittet af en værdi, der er baseret på den modtager opdateringer konstant. For at gøre dette, skal den have det aktuelle sæt af indgående anmodninger skal proces, samt den aktuelle gennemsnit. Alle de tjenester, der henter, behandler og gemmer oplysninger i en ekstern store (som en Azure blob eller tabel store i dag) er med høj sikkerhed. Det holder lige dens tilstand i store eksterne tilstand.

De fleste tjenester store i dag deres tilstand eksternt, fordi det eksterne lager Hvad indeholder pålidelighed, tilgængelighed, skalerbarhed og konsistens til tilstanden. I tjenesten strukturen ikke er med høj sikkerhed services påkrævet for at gemme deres tilstand eksternt, f.eks. Tjenesten strukturen sig tager af disse krav til både tjenestekode og -tjenestetilstand.

Antag, at vi vil skrive en tjeneste, der tager anmodninger om en serie af konverteringer, der skal udføres på et billede, og det billede, der skal konverteres.  For denne tjeneste, vil den returnerer en kommunikation-lytter (Lad os sige Web API), der åbnes en kommunikationsport og giver mulighed for anmodninger via en API som `ConvertImage(Image i, IList<Conversion> conversions)`. I denne API, kan tjenesten tage oplysninger, og Gem anmodningen i en pålidelig kø og derefter returnere nogle token til klienten, så den kan holde styr på anmodningen (da anmodninger kan gå et stykke tid).

I denne tjeneste kan RunAsync være mere komplekse. Tjenesten kan have en løkke i dets RunAsync, der trækker anmodninger af IReliableQueue, udfører konverteringer vises og gemmer resultatet i IReliableDictionary, så når klienten kommer tilbage, de kan få adgang til deres konverterede billeder. For at sikre, at selvom noget mislykkes billedet ikke tabt, denne pålidelig Service vil trække ud af køen, udføre konverteringerne og gemme resultatet i en transaktion. I dette tilfælde meddelelsen fjernes faktisk kun fra køen og resultaterne er gemt i ordbogen med resultat, når konverteringerne er fuldført. Hvis noget mislykkes i midten (såsom denne forekomst af koden, der kører på computeren), forbliver anmodningen i køen venter på at behandles igen.

Én ting at være opmærksom på om denne tjeneste er, at det lyder som en normal .NET-tjeneste. Den eneste forskel er, at de datastrukturer bruges (IReliableQueue og IReliableDictionary) der leveres af tjenesten strukturen, og derfor er foretaget meget pålidelige, tilgængelige og ensartede.

## <a name="when-to-use-reliable-services-apis"></a>Hvornår skal jeg bruge pålidelig Services API'er
Hvis et af følgende beskrive programmets servicebehov, bør du overveje pålidelig Services API'er:

- Du skal angive programmets funktionsmåde på tværs af flere enheder af tilstand (f.eks., ordrer og linjeelementer rækkefølge).

- Tilstanden dit program kan dannes naturligt som pålidelig ordbøger og køer.

- Din tilstand skal være meget tilgængeligt med lav ventetid adgang.

- Dit program skal styre på dokumentsammenfald eller granularitet overførte handlinger på tværs af en eller flere af pålidelige websteder.

- Du vil administrere kommunikation eller styre det partitionsskema til din tjeneste.

- Din kode skal have en ledig-threaded runtime-miljø.

- Dit program skal oprette eller destroy pålidelig ordbøger eller køer på kørselstidspunktet dynamisk.

- Du har brug for til at styre Service-strukturen har udleveret sikkerhedskopi og gendanne funktioner for din tjenestens tilstand * fra et program.

- Dit program skal bruge til at vedligeholde ændringsoversigt for dens enheder af tilstand *.

- Du vil udvikle eller forbruger tredje part udviklede, brugerdefinerede tilstand udbydere *.

> [AZURE.NOTE] * Funktioner er tilgængelige på SDK generelt tilgængelig.


## <a name="next-steps"></a>Næste trin
+ [Pålidelige Services Hurtig start](service-fabric-reliable-services-quick-start.md)
+ [Pålidelige Services avancerede brugen](service-fabric-reliable-services-advanced-usage.md)
+ [Pålidelige aktører programming modellen](service-fabric-reliable-actors-introduction.md)
