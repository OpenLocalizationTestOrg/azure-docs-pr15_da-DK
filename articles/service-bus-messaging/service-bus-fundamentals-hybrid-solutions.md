<properties 
    pageTitle="Azure Service Bus | Microsoft Azure" 
    description="En introduktion til brug af Service Bus til at oprette forbindelse Azure-programmer til andre programmer." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/31/2016" 
    ms.author="sethm"/>

# <a name="azure-service-bus"></a>Azure Service Bus

Om et program eller en tjeneste kører i skyen eller lokalt, skal den ofte interagere med andre programmer eller tjenester. For at give et bredt nyttig måde at gøre dette, tilbyder Microsoft Azure Service Bus. Denne artikel fører nærmere på denne teknologi, der beskriver den og hvorfor du muligvis vil bruge den.

## <a name="service-bus-fundamentals"></a>Service Bus grundlæggende

Forskellige situationer Ring for forskellige typer kommunikation. Nogle gange, er så programmer sende og modtage meddelelser via en enkelt kø den bedste løsning. En almindelig kø i andre situationer kan ikke nok; en kø med en Publicer og abonnere ordning er bedre. I nogle tilfælde er alle, der har virkelig er behov for en forbindelse mellem programmer. køer er ikke nødvendigt. Service Bus indeholder alle tre muligheder, så dine programmer til at interagere på flere forskellige måder.

Tjenesten Bus er en med flere lejer skybaseret tjeneste, hvilket betyder, at tjenesten er fælles for flere brugere. Hver bruger, som en udvikler programmet på computeren, opretter et *navneområde*, og derefter definerer funktioner til kommunikation hun har brug for i pågældende navneområdet. Figur 1 viser, hvordan det ser ud.

![][1]
 
**Figur 1: Service Bus udbyder med flere arkitekturer til at oprette forbindelse programmer via skyen.**

Du kan bruge en eller flere forekomster af fire forskellige kommunikation mekanismer, hver især forbinder programmer på en anden måde i et navneområde. Valgmulighederne er:

- *Køer*, som tillader en tovejskommunikation. Hver kø fungerer som mellemled (også kaldet en *broker*), der gemmer sendte meddelelser, indtil de modtages. Hver meddelelse modtages af en enkelt modtager.
- *Emner*, som understøtter én tovejskommunikation ved hjælp af *abonnementer*- et enkelt emne kan have flere abonnementer. Et emne, der fungerer som en mægler som en kø, men hvert abonnement kan du kan også bruge et filter til at modtage kun meddelelser, der opfylder bestemte kriterier.
- *Relæer*, som understøtter tovejskommunikation. I modsætning til køer og emner gemme en relay ikke startet meddelelser. det er ikke en mægler. I stedet overfører den kun dem til destinationsprogrammet.

Når du opretter en kø, emne eller relay, skal give du den et navn. Dette navn opretter kombineres med uanset du kaldet din navneområde, et entydigt id for objektet. Programmer kan give Service Bus dette navn og derefter bruge den kø, emne eller relay til at kommunikere med hinanden. 

Hvis du vil bruge en af disse objekter i scenariet relay, kan Windows-programmer bruge Windows Communication Foundation (WCF). Windows-programmer kan bruge Service Bus brugerdefineret SMS API'er for køer og emner. Hvis du vil gøre disse objekter nemmere at bruge fra Windows-programmer, yder Microsoft SDK'er til Java, Node.js og andre sprog. Du kan også åbne køer og emner med REST API'er via HTTP (s). 

Det er vigtigt at forstå, selvom Service Bus selve kører i skyen (det vil sige, i Microsoft-Azure datacentre), programmer, der bruger den kan køre overalt. Du kan bruge Service Bus til at oprette forbindelse programmer, der kører på Azure, for eksempel eller programmer, der kører i din egen datacenter. Du kan også bruge den til at oprette forbindelse et program, der kører på Azure eller en anden skybaseret platform med et lokalt program eller tablets og -telefoner. Det er også muligt at oprette forbindelse husholdningsapparater, sensorer og andre enheder til en central program eller en anden. Tjenesten Bus er en kommunikation ordning i skyen, der er tilgængelige fra stort set hvor som helst. Hvordan du bruger afhænger det af hvad programmerne skal gøre.

## <a name="queues"></a>Køer

Antag, at du beslutter dig for at forbinde to programmer ved hjælp af en Service Bus kø. Figur 2 illustrerer denne situation.

![][2]
 
**Figur 2: Service Bus køer giver envejs asynkron queuing.**

Det er nemt: en afsender sender en meddelelse til en tjeneste Bus kø, og en modtager henter meddelelsen på et senere tidspunkt. En kø kan have blot en enkelt modtager, som viser figur 2. Eller flere programmer kan læse fra den samme kø. I den sidste situation læses hver meddelelse af kun én modtager. Du skal bruge et emne i stedet for en flere støbt tjeneste.

Hver meddelelse består af to dele: et sæt af egenskaber, hver et tasten/værdi-par, og en binær meddelelsesteksten. Hvordan de bruges, afhænger af, hvad en programmet forsøger at gøre. For eksempel et program, der sender en meddelelse om et seneste salg kan indeholde egenskaberne *sælger = "Ava"* og *beløb = 10000*. Meddelelsesteksten kan indeholde et scannet billede af det salg underskrevet kontrakt eller, hvis der ikke er et, forbliver lige tomme.

En modtager kan læse en meddelelse fra en tjeneste Bus kø på to forskellige måder. Den første indstilling, kaldet *ReceiveAndDelete*, fjerner meddelelsen fra køen og sletter det med det samme. Dette er enkel, men hvis modtageren går ned, før den har afsluttet behandlingen meddelelsen, meddelelsen, går tabt. Da den er blevet fjernet fra køen, kan ingen andre modtager åbne den. 

Den anden mulighed, *PeekLock*, er meningen med hjælp til dette problem. Sådan **ReceiveAndDelete**fjerner **PeekLock** læse en meddelelse fra køen. Det slettet ikke meddelelsen. I stedet den låser den meddelelse, så det usynlige til andre modtagere, og derefter venter på en af tre ting:

- Hvis modtageren har behandlet meddelelsen korrekt, kalder **færdigt**og køen sletter meddelelsen. 
- Hvis modtageren beslutter, at den ikke behandle meddelelsen korrekt, kalder **afbryde**. Køen derefter fjerner låsen fra meddelelsen og gør den tilgængelig for andre modtagere.
- Hvis modtageren ringer ingen af disse inden for en konfigurerbar tid (som standard 60 sekunder), antager køen modtageren mislykkes. I dette tilfælde fungerer som om modtageren havde kaldet **afbryde**, gøre meddelelsen tilgængelige for andre modtagere.

Bemærk, at det kan ske, her: den samme meddelelse kan blive leveret to gange, måske til to forskellige modtagere. Programmer, der bruger Service Bus køer skal være forberedt på dette. Meddelelsen er læst fra en kø for at gøre det nemmere, hver meddelelse med en entydig **MessageID** egenskab, der som standard forbliver de samme, uanset hvor mange gange dubletter. 

Køer er nyttig i en hel situationer. De kan programmer til at kommunikere, selv når begge ikke kører på samme tid, noget, som er især praktiske med batchen og mobilprogrammer. En kø med flere modtagere indeholder også automatisk belastningsjustering, da sendte meddelelser er udbrede disse modtagere.

## <a name="topics"></a>Emner

Nyttige, som de er, køer ikke altid den rigtige løsning. Nogle gange er Service Bus emner bedre. Figur 3 illustrerer denne ide.

![][3]
 
**Figur 3: Baseret på det filter, der angiver et abonnerende til computeren, kan det modtage nogle eller alle de meddelelser, der er sendt til en tjeneste Bus emne.**

Et *emne* , der svarer til en kø på mange måder. Afsendere Send meddelelser til et emne på samme måde, de sende meddelelser til en kø, og disse meddelelser se den samme som i køer. Den eneste forskel er, at emner aktiverer hvert modtagelse program for at oprette et nyt *abonnement* ved at definere et *filter*. Et abonnement vises derefter kun de meddelelser, der svarer til filteret. Figur 3 vises for eksempel en afsender og et emne med tre abonnenter, hver med sin egen filter:

- Abonnement 1 modtager kun meddelelser, der indeholder egenskaben *sælger = "Ava"*.
- Abonnement 2 modtager meddelelser, der indeholder egenskaben *sælger = "Fonetisk"* og/eller indeholder et *beløb* egenskab, hvis værdien er større end 100,000. Fonetisk er måske salgschef, så hun vil se både sin egen salg og alle stor salg uanset hvem opretter dem.
- Abonnement 3 har angivet dens filter til *Sand*, hvilket betyder, at den modtager alle meddelelser. For eksempel dette program kan være ansvarlig for at vedligeholde en overvågningslog og derfor det skal se alle meddelelser.

Som med køer, kan abonnenter til et emne, læse meddelelser ved hjælp af **ReceiveAndDelete** eller **PeekLock**. I modsætning til køer, men kan en enkelt meddelelse, der sendes til et emne modtages af flere abonnementer. Denne metode, også kaldet *Publicer og Abonner* (eller *pub/sub*), er nyttig, når flere programmer er interesseret i de samme meddelelser. Ved at definere filteret højre, Udnyt hver abonnement del af strømmen meddelelse, som det skal se.

## <a name="relays"></a>Relæer

Både køer og emner indeholder envejs asynkron kommunikation via en mægler. Trafik løber i kun én retning, og der er ingen direkte forbindelse mellem afsendere og modtagere. Men hvad nu, hvis du ikke ønsker dette? Antag, at dine programmer skal både sende og modtage meddelelser, eller måske ønskede et direkte link mellem dem og du ikke har brug for en mægler til lagring af meddelelser. Til adresse scenarier som dette, Service Bus yder *videresender*, som viser figur 4.

![][4]
 
**Figur 4: Service Bus relay indeholder synkron, tovejs kommunikation mellem programmer.**

De indlysende spørgsmål til at spørge om relæer er dette: Hvorfor skal jeg bruge en? Selvom jeg ikke har brug for køer, hvorfor gøre programmer kommunikere via en skybaseret tjeneste i stedet for kun interagere direkte? Svaret er, at tale direkte kan være sværere, end du tror.

Antag, at du vil oprette forbindelse to lokale programmer, kører begge i virksomhedens datacentre. Hver af disse programmer bagved en firewall, og hver datacenter bruger sandsynligvis NAT (NAT). Firewallen blokerer indgående data på alle, men nogle porte og NAT indebærer, at hvert program, der kører på computeren ikke har en fast IP-adresse, som du kan få fat direkte fra uden for datacenteret. Uden nogle ekstra Hjælp er forbindelse disse programmer via det offentlige internet problematisk.

En Service Bus relay kan hjælpe dig. For at kommunikere bi directionally gennem en relay, hvert program, der opretter en udgående TCP-forbindelse med tjenesten Bus og derefter holder den åben. Al kommunikation mellem de to programmer varer over disse forbindelser. Da der blev oprettet fra hver forbindelse i datacenteret, giver firewallen indgående trafik til hvert program uden at åbne nye porte. Denne metode får også løse problemet NAT, fordi hvert program har et ensartet slutpunkt i skyen i hele kommunikationen. Programmerne kan undgå de problemer, der ville have foretaget kommunikation svært ved at udveksle data via videresendelse. 

Hvis du vil bruge Service Bus relæer, afhængige programmer på Windows Communication Foundation (WCF). Service Bus indeholder WCF bindinger, som gør det nemt for Windows-programmer til at kommunikere via relæer. Programmer, der allerede bruger WCF kan typisk kun angive en af disse bindinger og derefter tale med hinanden via en relay. I modsætning til køer og emner kræver ved hjælp af relæer fra Windows-programmer, mens du muligt, dog nogle programming indsats der findes ingen standard biblioteker.

I modsætning til køer og emner Opret programmer ikke eksplicit relæer. I stedet, når et program, som ønsker at modtage meddelelser opretter TCP-forbindelse til tjenesten Bus, en relay oprettes automatisk. Når forbindelsen bliver afbrudt, slettes videresendelse. Hvis du vil aktivere et program til at finde den relay, der er oprettet af en bestemt lytter, indeholder Service Bus en registreringsdatabasen, som gør det muligt for programmer til at finde en bestemt relay ved navn.

Relæer er den rigtige løsning, når du har brug for direkte kommunikation mellem programmer. For eksempel bør du overveje en flyselskaber reservationssystem, der kører i et lokalt datacenter, der skal åbnes fra indtjekning kiosker, mobilenheder og andre computere. Programmer, der kører på alle disse systemer kan stole på Service Bus relæer i skyen for at kommunikere, uanset hvor de kan køre.

## <a name="summary"></a>Oversigt

Forbinder programmer er altid en del af opbygning af løsninger, der er fuldført, og området af scenarier, der kræver programmer og tjenester til at kommunikere med hinanden er indstillet til at øge som flere programmer og enheder er tilsluttet internettet. Ved at indsende skybaseret teknologier for at opnå dette via køer, emner og relæer, Service Bus har til formål at gøre dette væsentlige funktionen nemmere at implementere og mere alment tilgængelig.

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende af Azure Service Bus, skal du følge disse links for at få mere at vide.

- Sådan bruger du [Service Bus køer](service-bus-dotnet-get-started-with-queues.md)
- Sådan bruger du [Service Bus emner](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- Sådan bruger du [Service Bus relay](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
- [Service Bus eksempler](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png
