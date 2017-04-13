<properties
    pageTitle="At trække i offentligt tilgængelige data til Azure begivenhed Hubs | Microsoft Azure"
    description="Oversigt over begivenhed hubberne importere fra web eksempel"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>At trække i offentligt tilgængelige data til Azure begivenhed Hubs

I scenarier med typisk Internet af ting (IoT) har du enheder, du kan programmere opslagsnål data til Azure, enten til en Azure begivenhed Hub eller en IoT hub. Begge disse hubs er indgangspunkter til Azure til lagring, analysere og visualisere med en lang række funktioner, der er gjort tilgængelige på Microsoft Azure. Men de begge kræver, at du push data i dem, der er formateret som JSON og sikret på bestemte måder. Dette viser følgende spørgsmål. Hvad skal du gøre, hvis du vil hente data fra offentlige og private datakilder, hvor data, der vises som en webtjeneste eller feed nogle sortering, men du har ikke mulighed for at ændre, hvordan dataene er blevet publiceret? Overvej at vejrudsigt eller trafik eller aktiekurser - du kan ikke se NOAA, eller WSDOT eller NASDAQ til at konfigurere en opslagsnål til begivenhed-Hub. For at løse problemet, har vi skrevet og Åbn leveres et lille cloud-eksempel, der kan du redigere og installere, der henter data fra en sådan kilde og push til begivenhed-Hub. Derfra kan du gøre det, du vil med den, underlagt naturligvis til licensvilkårene fra producenten. Du kan finde programmet, [her](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Bemærk, at koden i dette eksempel kun viser, hvordan du kan trække data fra typisk web feeds, og hvordan du kan skrive en Azure begivenhed hub. Dette er ikke beregnet til at være et fremstilling program, og er blevet ikke forsøgt at gøre det passer til brug i sådanne et miljø – det er strictfly en DIY henvender sig til udviklere eksempel kun. Desuden er eksistensen af dette eksempel ikke støttelicensen en anbefaling, skal du **hente** data til Azure i stedet for **push** den. Du skal gennemgå sikkerhed, ydeevne og funktionalitet, og omkostninger faktorer inden du sætter dig på en arkitektur til slut.

## <a name="application-structure"></a>Programmet struktur

Programmet er skrevet i C#, og [eksempel beskrivelse](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) indeholder alle de oplysninger, du vil redigere, oprette og publicere programmet. De følgende afsnit indeholder en overordnet oversigt over, hvad betyder det programmet.

Vi begynder med den antagelse, at du har adgang til et datafeed. For eksempel kan du trække i trafik dataene fra Washington tilstand afdeling af transport eller vejrudsigt dataene fra NOAA, enten for at vise brugerdefinerede rapporter eller til at kombinere disse data med andre data i dit program. Skal du også har konfigureret en Azure begivenhed Hub, og ved forbindelsen streng, der er behov for at få adgang til den.

Når GenericWebToEH løsningen starter, læser en konfigurationsfil (App.config) for at få en række ting:

1. URL-adressen, eller en liste over URL-adresser, til webstedet for publicering af dataene. Ideelt set dette er et websted, hvor der publiceres data i JSON, som dem, der refereres til af WSDOT [her](http://www.wsdot.wa.gov/Traffic/api/). 
2. Legitimationsoplysninger for URL-adressen, hvis det er nødvendigt. Mange offentlige kilder kræver ikke legitimationsoplysninger, eller du kan anbringe legitimationsoplysningerne i URL-strengen. Andre kræver, at du angiver separat. (Bemærk, at du kan kun angive ét sæt legitimationsoplysninger i dette program, så det virker kun, hvis du angiver kun én URL-adresse, ikke en liste over URL-adresser).
3. Forbindelsesstreng, og navnet på begivenheden hubben i pågældende begivenhed Hubs navneområdet, du sender dataene. Du kan finde disse oplysninger i portalen Azure.
4. En sengetider tidsinterval, i millisekunder, hvor intervallet mellem forespørgsel blandt webstedet offentligt tilgængelige data. Værdien indstilles kræver nogle tanken. Hvis du afstemning for sjældent, kan du mister data. på den anden side, hvis du afstemning for ofte, kan du få en masse gentagne data eller dårligere endnu, du kan også blokeres som en nefarious bot. Overvej, hvor ofte datakilden opdateres - vejr eller trafik data kan opdateres hver 15 minutter, men aktiekurser måske alle par sekunder, afhængigt af hvor du får dem. 
5. Et flag for at se programmet, om dataene kommer som JSON eller XML. Programmet, da du har brug for til at overføre data til en begivenhed-Hub, har et modul til at konvertere XML til JSON før afsendelse.

Når du har læst konfigurationsfilen, går programmet i en løkke - adgang til det offentlige websted, konvertering af data, hvis det er nødvendigt, skrive den til din begivenhed-Hub, og derefter venter sengetider interval, før du udfører den forfra igen. Mere specifikt:

  * Læse det offentlige websted. Forekomsten af RawXMLWithHeaderToJsonReader klasse bruges til modtagelse klar til at sende data fra Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Den læser kilde stream i metoden GetData() og deler den med GetXmlFromOriginalText på mindre dele (det vil sige records). 
  Denne metode kan læse XML samt korrekt udformet JSON eller JSON matrix. Behandling af derefter startes ved hjælp af MergeToXML konfiguration fra App.config (standard = tom).
  * Modtagelse og afsendelse dataene er implementeret som en løkke i metoden Process() i Program.cs. 
  Når du modtager outputresultater fra GetData(), adskilt metode enqueues værdier til begivenhed-Hub.

## <a name="next-steps"></a>Næste trin

Hvis du vil installere løsningen, klone eller hente programmet [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , redigere App.config-filen, opbygge den og til sidst publicere den. Når du har udgivet programmet, kan du se den kører i Azure klassisk portal under Cloud Services, og du kan ændre nogle af konfigurationsindstillingerne (som mål-begivenhed Hub og sengetider interval) under fanen **Konfigurer** .

Se flere eksempler begivenhed Hubs i [Galleri med Azure eksempler](https://azure.microsoft.com/documentation/samples/?service=event-hubs) og på [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).
