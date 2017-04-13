<properties 
    pageTitle="Begivenhed Hubs ofte stillede spørgsmål (FAQ) | Microsoft Azure"
    description="Begivenhed Hubs ofte stillede spørgsmål."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>Begivenhed Hubs ofte stillede spørgsmål

Begivenhed Hubs indeholder store indtag, brugerdata og behandling af datahændelser fra høj overførselshastighed datakilder og/eller millioner af enheder. Når parvis med Service Bus køer og emner, kan begivenhed Hubs fast styrings- og installationer for [Internet af ting (IoT)](https://azure.microsoft.com/services/iot-hub/) scenarier.

Denne artikel beskrives prisoplysninger og svar på nogle ofte stillede spørgsmål om begivenhed Hubs:

## <a name="pricing-information"></a>Prisoplysninger

Du kan finde detaljerede oplysninger om begivenhed Hubs priser, [begivenhed Hubs prisoplysninger](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="how-are-event-hubs-ingress-events-calculated"></a>Hvordan beregnes begivenhed Hubs vandindtrængen begivenheder?

Hver enkelt hændelse, der er sendt til en begivenhed Hub tæller som en fakturerbar meddelelse. En *vandindtrængen begivenhed* er defineret som en enhed med data, som er mindre end eller lig med 64 KB. En hvilken som helst begivenhed, der er mindre end eller lig med 64 KB i størrelse anses for at være en fakturerbar hændelse. Hvis begivenheden er større end 64 KB, beregnes antallet af fakturerbar begivenheder efter begivenhed størrelsen i multipla af 64 KB. For eksempel en 8 KB begivenhed, der er sendt til begivenhed hubben faktureret som en begivenhed, men en 96 KB-meddelelse, der er sendt til begivenhed hubben faktureres som to begivenheder.

Hændelser consumed fra en hændelse-Hub, som samt administration handlinger og styre opkald som kontrolpunkter, tælles ikke som fakturerbar vandindtrængen begivenheder, men periodiseres op til tilladelsen overførselshastighed enhed.

## <a name="what-are-event-hubs-throughput-units"></a>Hvad er begivenhed Hubs overførselshastighed enheder?

Du vælge eksplicit begivenhed Hubs overførselshastighed enheder, enten via portalen Azure eller begivenhed Hubs ressource manager skabeloner. Overførselshastighed enheder gælder for alle begivenhed hubber i en begivenhed Hubs navneområde, og hver overførselshastighed enhed berettiger navneområdet til følgende funktioner:

- Op til 1 MB sekundet af vandindtrængen begivenheder (hændelser, der er sendt til en begivenhed Hub), men ingen mere end 1000 vandindtrængen begivenheder, management handlinger eller kontrolelement opkald API sekundet.

- Op til 2 MB sekundet udgangspunkt begivenheder (hændelser consumed fra en hændelse Hub).

- Op til 84 GB lagerplads begivenhed (tilstrækkelige til standard 24-timers opbevaringsperiode).

Begivenhed Hubs overførselshastighed enheder faktureres hver time, baseret på det maksimale antal enheder, der er valgt under den angivne time.

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Sådan gennemtvinges begivenhed Hubs overførselshastighed enhed begrænsninger?

Hvis den samlede vandindtrængen gennemløb eller den samlede vandindtrængen begivenhed rente på tværs af alle begivenhed hubber i et navneområde overstiger Sammenlæg overførselshastighed enhed kvoter, afsendere sænkes og modtage fejl, der angiver, at vandindtrængen kvoten er overskredet.

Hvis den samlede udgangspunkt gennemløb eller den samlede begivenhed udgangspunkt rente på tværs af alle begivenhed hubber i et navneområde overstiger Sammenlæg overførselshastighed enhed kvoter, modtagere er begrænset og modtage fejl, der angiver, at kvoten udgangspunkt er overskredet. Indgangs- og udgangspunkt kvoter, håndhæves separat, så ingen afsender kan medføre begivenhed forbrug til langsommere, og ikke nødvendigvis kan forhindre en modtager begivenheder i at blive sendt til en begivenhed Hub.

Bemærk, at valgmuligheden overførselshastighed enhed uafhængigt af antallet af begivenhed Hubs partitioner. Hver partition tilbyder en maksimale antal gennemløb af 1 MB til hver anden vandindtrængen (med maksimalt 1000 begivenheder sekundet) og 2 MB til hver anden udgangspunkt, men der er gratis fast for partitionerne sig selv. Det er for aggregeret overførselshastighed enheder på alle begivenhed hubber i en begivenhed Hubs navneområde. Med denne mønster, kan du oprette nok partitioner at understøtte den forventede største belastning for deres systemer, uden at der påløber eventuelle overførselshastighed enhed gebyrer, indtil begivenhed belastningen på systemet faktisk kræver højere overførselshastighed tal, og uden at ændre strukturen og arkitekturen i dine systemer som belastning på systemet øges.

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Er der en grænse på antallet af overførselshastighed enheder, der kan udvælges?

Der findes en standard kvote af 20 overførselshastighed enheder per navneområde. Du kan anmode om en større kvote af overførselshastighed enheder ved at arkivere en supportbilletter. Ud over grænsen på 20 overførselshastighed enhed er pakker tilgængelige i 20 og 100 overførselshastighed enheder. Bemærk, at ved at bruge mere end 20 overførselshastighed enheder fjernes muligheden for at ændre antallet af overførselshastighed enheder uden arkivering af en supportbilletter.

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Er der et gebyr for at bevare begivenhed Hubs begivenheder i mere end 24 timer?

Begivenhed Hubs Standard niveauet tillader, at meddelelsen opbevaring perioder, der er mere end 24 timer for op til 30 dage. Hvis størrelsen af det samlede antal gemte hændelser overskrider lagerplads ydelse til antallet markerede overførselshastighed enheder (84 GB per overførselshastighed enhed), registreres den størrelse, der overskrider tilladelsen til den publicerede Azure Blob storage pris. Lagerplads ydelse i hver overførselshastighed enhed dækker alle lagerplads omkostninger for opbevaring perioder af 24 timer (standard) Selvom overførselshastighed måleenheden bruges til maksimalt vandindtrængen ydelse.

## <a name="what-is-the-maximum-retention-period"></a>Hvad er den maksimale opbevaringsperiode?

Begivenhed Hubs Standard niveau understøtter i øjeblikket en maksimale opbevaringsperiode af 7 dage. Bemærk, at begivenhed Hubs ikke er beregnet som et permanent datalager. Opbevaring perioder, der er større end 24 timer er beregnet til scenarier, hvor det er praktisk at afspille en begivenhed stream i de samme systemer for eksempel, at undervise eller bekræfte en ny computer learning model på eksisterende data.

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hvordan lagerstørrelse begivenhed Hubs beregnes, og betale?

Den samlede størrelse af alle gemte hændelser, herunder eventuelle interne omkostninger for begivenhed sidehoveder eller på disk lagerplads strukturer i alle begivenhed hubber måles i løbet af arbejdsdagen. I slutningen af dag beregnes lagerstørrelse spidsbelastning. De daglige lagerplads ydelse er beregnes ud fra det mindste antal overførselshastighed enheder, der er valgt under den dag (hver overførselshastighed enhed giver en justering af 84 GB). Hvis den samlede størrelse overstiger den beregnede daglige lagerplads ydelse, faktureres unødvendig opbevaring ved hjælp af Azure Blob storage satser (i den **Lokalt overflødige lagerplads** rente).

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>Kan jeg bruge en enkelt AMQP forbindelse til at sende og modtage fra begivenhed Hubs og Service Bus køer/emner?

Ja, så længe begivenhed Hubs, forespørgsler og emner, der er i det samme navneområde. Så kan du implementere tovejs-, formidlet forbindelse til mange enheder med subsecond latenstider i en økonomisk og meget SVG måde.

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Gælder formidlet forbindelse gebyrer for begivenhed Hubs?

Forbindelse gebyrer gælder kun, når AMQP protokollen bruges til afsendere. Der er ingen forbindelse gebyrer for afsendelse af hændelser ved hjælp af HTTP, uanset antallet af afsendelse af systemer eller enheder. Hvis du planlægger at bruge AMQP (for eksempel, at opnå mere effektiv begivenhed streaming eller for at aktivere tovejskommunikation i IoT styrings- og scenarier), kan du se siden [Service Bus prisoplysninger](https://azure.microsoft.com/pricing/details/service-bus/) for oplysninger om, hvad der udgør en formidlet forbindelse, og hvordan de er forbrugsafregnede.

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Hvad er forskellen mellem begivenhed Hubs grundlæggende og Standard niveauer?

Begivenhed Hubs Standard niveau indeholder funktioner ud over, hvad der er tilgængeligt i begivenhed Hubs grundlæggende og i nogle Konkurrentanalyse systemer. Disse funktioner omfatter opbevaring perioder af mere end 24 timer og muligheden for at bruge en enkelt AMQP forbindelse til at sende kommandoer til et stort antal enheder med subsecond latenstider samt til at sende telemetri fra disse enheder til begivenhed Hubs. Du kan finde på listen over funktioner, [begivenhed Hubs prisoplysninger](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="geographic-availability"></a>Geografiske tilgængelighed

Begivenhed Hubs er tilgængelig i følgende områder:

|Geografisk|Områder|
|---|---|
|USA|Centrale USA af US af US 2, syd centrale USA, og Vest USA|
|Europe|Nord Europe, vest Europe|
|Asien/Stillehavsområdet|Sydøstasien, Sydøstasien|
|Japan|Japan Øst Japan vest|
|Brasilien|Brasilien syd|
|Australien|Australien Øst, Australien Sydøst|

## <a name="support-and-sla"></a>Support og SERVICENIVEAUAFTALE

Teknisk support til begivenhed Hubs er tilgængelig via [communityforummer](https://social.msdn.microsoft.com/forums/azure/home). Support til fakturering og abonnement management leveres uden omkostninger.

Hvis du vil vide mere om vores SERVICENIVEAUAFTALE, får du vist siden [Serviceaftaler niveau](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om begivenhed Hubs skal du se følgende artikler:

- [Oversigt over begivenhed Hubs][].
- En komplet [Northwind, der bruger begivenhed Hubs][].

[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
