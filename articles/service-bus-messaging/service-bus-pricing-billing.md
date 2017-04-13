<properties 
    pageTitle="Service Bus priser og fakturering | Microsoft Azure"
    description="Oversigt over Service Bus priser struktur."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/06/2016"
    ms.author="sethm" />

# <a name="service-bus-pricing-and-billing"></a>Service Bus priser og fakturering

Service Bus tilbydes i Basic, som Standard og [Premium](service-bus-premium-messaging.md) niveauer. Du kan vælge et webtjenesten for hver tjeneste Bus service navneområde, som du opretter, og dette niveau valg gælder på tværs af alle enheder, der er oprettet i pågældende navneområdet.

>[AZURE.NOTE] Se [Azure Service Bus priser side](https://azure.microsoft.com/pricing/details/service-bus/)og [Ofte stillede spørgsmål om tjenesten Bus](service-bus-faq.md#service-bus-pricing)kan finde detaljerede oplysninger om aktuelle Service Bus priser.

Service Bus bruger følgende to målere til køer og emner/abonnementer:

1. **Messaging handlinger**: defineret som API-kald mod kø eller emne/abonnement slutpunkter for tjenesten. Dette tæller erstatter meddelelser sendes eller modtages som den primære enhed fakturerbar anvendelsesområder køer og emner/abonnementer.

2. **Formidlet forbindelser**: defineret som det højeste antal forbindelser persistent åbne mod køer, emner eller abonnementer under en bestemt time prøveperiode. Dette tæller gælder kun i den almindelige lag, hvor du kan åbne flere forbindelser (tidligere forbindelser er begrænset til 100 per emne-kø/abonnement) for et gebyr nominel hver forbindelse.

**Standard** niveauet introducerer graduerede priser for handlinger, der udføres med køer og emner/abonnementer, hvilket resulterer i beholdning-baserede rabatter på op til 80% med det højeste niveau af brugen. Der er også et Standard niveau base gebyr på $10 månedsbasis, som gør det muligt at udføre handlinger op til 12.5 millioner månedsbasis gratis.

**Premium** niveau indeholder ressource isolationsniveauet på CPU og hukommelse lag, så hver kunde arbejdsbelastningen kører i isolationsniveauet. Denne ressource objektbeholder kaldes en *messaging enhed*. Hver premium navneområde fordeles mindst én SMS enhed. Du kan købe 1, 2 eller 4 SMS enheder for hver tjeneste Bus Premium navneområde. Et enkelt arbejdsbelastningen eller en enhed kan strækker sig over flere SMS enheder og antallet af beskeder enheder kan ændres efter behov, selvom fakturering er i 24-timers eller daglige rente gebyrer. Resultatet er mere forudsigelige og gentaget ydeevnen til din Service Bus-baseret løsning. Ikke kun er denne ydeevne mere forudsigelige og tilgængelig, men det er også hurtigere. Azure Service Bus Premium messaging bygger på lagerplads programmet blev introduceret i Azure begivenhed Hubs. Med Premium messaging er optimal ydeevne meget hurtigere end det Standard niveau.

Bemærk, at den standard base gebyr betale kun én gang månedsbasis per Azure-abonnement. Det betyder, at når du opretter en enkelt Standard eller Premium niveau Service Bus navneområde, du vil kunne oprette så mange flere Standard eller Premium niveau navneområder som du vil bruge, under den samme Azure-abonnement, uden at der påløber yderligere grundlæggende gebyrer.

Alle eksisterende Service Bus navneområder oprettet før 1 November 2014 er placeres automatisk i det Standard niveau. Dette sikrer, at du fortsat har adgang til alle funktioner, der er tilgængelige med Service Bus. Efterfølgende, kan du bruge [Azure klassisk portal][] for at nedgradere til de grundlæggende trin, hvis du ønsker.

Den følgende tabel opsummerer de funktionelle forskelle mellem de grundlæggende og Standard/Premium lag.

|Egenskab|Grundlæggende|Standard/Premium|
|---|---|---|
|Køer|Ja|Ja|
|Planlagte meddelelser|Ja|Ja|
|Emner/abonnementer|Nej|Ja|
|Relæer|Nej|Ja|
|Transaktioner|Nej|Ja|
|Ophævelse kopiering|Nej|Ja|
|Sessioner|Nej|Ja|
|Store meddelelser|Nej|Ja|
|ForwardTo|Nej|Ja|
|SendVia|Nej|Ja|
|Formidlet forbindelser (inkluderet)|100 per Service Bus navneområde|1.000 om Azure-abonnement|
|Formidlet forbindelser (overskud tilladt)|Nej|Ja (fakturerbar)|

## <a name="messaging-operations"></a>SMS handlinger

Som en del af den nye priser model ændres fakturering for køer og emner/abonnementer. Disse objekter skifter fra fakturering hver meddelelse til fakturering i handlingen. "Handlingen" refererer til en hvilken som helst API-opkald, der er foretaget mod en kø eller emne/abonnement tjenesteslutpunkt. Dette omfatter administration, send/modtag og session tilstand handlinger.

|Handlingstype|Beskrivelse|
|---|---|
|Administration|Oprette, læse, opdatere, slette (CRUD) mod køer eller emner/abonnementer.|
|Messaging|Sende og modtage meddelelser med køer eller emner/abonnementer.|
|Sessionstilstand|Få eller angive sessionstilstand på en kø eller emne/abonnement.|

Følgende priser er effektive starter 1 November 2014:

|Grundlæggende|Omkostninger|
|---|---|
|Handlinger|0,05 per millioner handlinger|

|Standard|Omkostninger|
|---|---|
|Grundlæggende gebyr|$10/ måned|
|Først 12.5 millioner handlinger/måned|Inkluderet|
|12.5-100 millioner handlinger/måned|0,80 per millioner handlinger|
|100 millioner - 2.500 millioner handlinger/måned|0,50 per millioner handlinger|
|Over 2.500 millioner handlinger/måned|$0,20 per millioner handlinger|

|Premium|Omkostninger|
|---|---|
|Daglig|$11.13 fast i hver meddelelse enhed|

## <a name="brokered-connections"></a>Formidlet forbindelser

*Brokered forbindelser* plads til kunde brugsmønstre, der involverer et stort antal "vedvarende forbindelse" afsendere/modtagere mod køer, emner eller abonnementer. Vedvarende forbundne afsendere/modtagere er dem, der har forbindelse med AMQP eller HTTP med en ikke-nul modtage timeout (for eksempel HTTP lang forespørgsel blandt). HTTP afsendere og modtagere med en øjeblikkelig timeout genererer ikke formidlet forbindelser.

Tidligere, køer og emner/abonnementer havde en begrænsning på 100 samtidige forbindelser per URL-adresse. Den aktuelle fakturering farveskema fjerner-Webadresse grænsen for køer og emner/abonnementer og implementerer kvoter og måling på formidlet forbindelser Service Bus navneområde og Azure abonnementsniveauer.

Det grundlæggende niveau omfatter og er begrænset til 100 formidlet forbindelser per Service Bus navneområde. Forbindelser over dette nummer afvises, i det grundlæggende niveau. Det Standard niveau fjerner grænsen for hver navneområde og tæller Sammenlæg formidlet forbindelse brugen på tværs af Azure abonnementet. I den almindelige lag tillades 1.000 formidlet forbindelser per Azure abonnement uden ekstra omkostninger (ud over den grundlæggende gebyr). Bruge mere end 1000 formidlet forbindelser alt på tværs af Standard-niveau Service Bus faktureres navneområder i et Azure-abonnement på en gradueret tidsplan, som vist i nedenstående tabel.

|Formidlet forbindelser (Standard lag)|Omkostninger|
|---|---|
|Første 1.000/måned|Inkluderet med grundlæggende gebyr|
|1.000-100.000/måned|0.03 per forbindelse/måned|
|100.000-500.000/måned|0.025 per forbindelse/måned|
|Over 500.000/måned|0.015 per forbindelse/måned|

>[AZURE.NOTE] 1.000 formidlet forbindelser, der følger med det almindelige SMS niveau (via den grundlæggende gebyr) og kan deles på tværs af alle køer, emner og abonnementer i det tilknyttede Azure abonnement.

<br />

>[AZURE.NOTE] Fakturering er baseret på det maksimale antal samtidige forbindelser og er proportionalt hver time baseret på 744 timer hver måned.

|Premium niveau
|---|
|Formidlet forbindelser betaler ikke i Premium niveauet.|

Se afsnittet [ofte stillede spørgsmål om](#faq) senere i dette emne kan finde flere oplysninger om formidlet forbindelser.

## <a name="relay"></a>Relay

Relæer er kun tilgængelig i Standard niveau navneområder. Ellers forbliver priser og forbindelse kvoter for relæer uændret. Det betyder, at relæer fortsætter med at betale på antallet af beskeder (ikke handlinger), og relay-timer.

|Overføre priser|Omkostninger|
|---|---|
|Relay timer|$0.10 for hver 100 relay-timer|
|Meddelelser|0,01 for hver 10.000 meddelelser|

## <a name="faq"></a>OFTE STILLEDE SPØRGSMÅL

### <a name="how-is-the-relay-hours-meter-calculated"></a>Hvordan beregnes tælleren Relay timer?

Se [dette emne](service-bus-faq.md#how-is-the-relay-hours-meter-calculated).

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Hvad er formidlet forbindelser og hvordan jeg få betale for dem?

En formidlet forbindelse er defineret som et af følgende:

1. En AMQP forbindelse fra en klient til en tjeneste Bus kø eller emne/abonnement.

2. En HTTP opkald for at modtage en meddelelse fra en tjeneste Bus emne eller kø, der indeholder en Modtag timeout-værdi, der er større end nul.

Service Bus gebyrer for spidsbelastning antallet af samtidige formidlet forbindelser, der overskrider inkluderet antallet (1.000 i den almindelige lag). Spidser er målt på grundlag af hver time, proportionalt ved at dividere med 744 timer i løbet af en måned eller adderet over den månedlige faktureringsperiode. Inkluderet antallet (1.000 formidlet forbindelser månedsbasis) anvendes i slutningen af den faktureringsperiode mod summen af proportionelt hver time spidser.

Eksempel:

1. Hver af 10.000 enheder opretter forbindelse via en enkelt AMQP forbindelse og modtager kommandoer fra en tjeneste Bus emne. Enhederne sende telemetri begivenheder til en begivenhed Hub. Hvis alle enheder oprette forbindelse til 12 timer hver dag, gælder følgende forbindelse omkostninger (ud over eventuelle andre Service Bus emne gebyrer): 10.000 forbindelser *12 timer* 31 dage / 744 = 5.000 formidlet forbindelser. Efter månedlig ydelse på 1.000 formidlet forbindelser, vil du betale for 4.000 formidlet forbindelser, til en sats på $0.03 per formidlet forbindelse til en total $120.

2. 10.000 enheder modtage meddelelser fra en tjeneste Bus kø via HTTP, der angiver timeout for en forskellig fra nul. Hvis alle enheder opretter forbindelse til 12 timer hver dag, får du vist følgende forbindelse omkostninger (ud over eventuelle andre Service Bus gebyrer): 10.000 HTTP modtage forbindelser *12 timer om dagen* 31 dage / 744 timer = 5.000 formidlet forbindelser.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Gælder formidlet forbindelse gebyrer for køer og emner/abonnementer?

Ja. Der er ingen forbindelse gebyrer for afsendelse af hændelser ved hjælp af HTTP, uanset antallet af afsendelse af systemer eller enheder. Modtage hændelser med HTTP ved hjælp af en timeout, der er større end nul, også kaldet "længe forespørge", genererer formidlet forbindelse gebyrer. AMQP forbindelser generere formidlet forbindelse gebyrer uanset om forbindelserne, der bliver brugt til at sende eller modtage. Bemærk, at 100 formidlet forbindelser er tilladt gratis i et grundlæggende navneområde. Dette er det maksimale antal formidlet forbindelser, der er tilladt for Azure abonnementet. De første 1.000 formidlet forbindelser på tværs af alle Standard navneområder i et Azure-abonnement er inkluderet gratis ekstra (ud over den grundlæggende gebyr). Da disse kvoter er nok til at dækker mange SMS-tjenester-scenarier, være formidlet forbindelse gebyrer som regel kun relevant, hvis du planlægger at bruge AMQP eller HTTP lang-afstemning med et stort antal klienter. for at opnå mere effektiv begivenhed streaming eller aktivere tovejskommunikation med mange enheder eller forekomster af tjenesteprogrammer.

## <a name="next-steps"></a>Næste trin

- Få mere at vide om Service Bus priser se [Azure Service Bus priser side](https://azure.microsoft.com/pricing/details/service-bus/).

- Se [Ofte stillede spørgsmål om tjenesten Bus](service-bus-faq.md#service-bus-pricing) til nogle almindelige ofte stillede spørgsmål om tjenesten bus priser og fakturering.

[Azure klassisk portal]: http://manage.windowsazure.com