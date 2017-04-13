<properties 
    pageTitle="Isolations Service Bus programmer mod afbrydelser og nedbrud | Microsoft Azure"
    description="I denne artikel beskrives teknikker, du kan bruge til at beskytte programmer mod en potentiel Service Bus afbrydelse."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Bedste fremgangsmåder til Isolations programmer mod Service Bus udfald og nedbrud

Kritiske programmer skal fungere løbende, også ved tilstedeværelse af ikke-planlagt udfald eller nedbrud. Dette emne beskrives teknikker, du kan bruge til at beskytte Service Bus programmer med en mulig service afbrydelse eller nedbrud.

En afbrydelse er defineret som Azure Service Bus midlertidige hvorfor. Afbrydelse kan påvirke visse komponenter i tjenesten Bus, som en SMS butik eller endda hele datacenter. Når problemet er løst, bliver Service Bus tilgængelig igen. Typisk medføre en afbrydelse ikke tab af meddelelser eller andre data. Et eksempel på en komponentfejl er en bestemt SMS butik hvorfor. Et eksempel på et datacenter hele afbrydelse er en strømafbrydelse af datacenteret eller en defekt datacenter netværks-switch. En afbrydelse kan sidste fra et par minutter til et par dage.

Nedbrud er defineret som på permanent tab af en enhed for tidsskala Service Bus eller datacenter. Datacenteret kan eller bliver ikke tilgængelig igen. Typisk medfører nedbrud tab af nogle eller alle meddelelser eller andre data. Eksempler på nedbrud er brand, oversvømmelse eller jordskælv.

## <a name="current-architecture"></a>Aktuelle arkitektur

Service Bus bruger flere SMS butikker til lagring af meddelelser, der sendes til køer eller emner. En ikke-opdelt kø eller emne er tildelt til en SMS store. Hvis denne SMS store er tilgængelig, mislykkes alle handlinger på den pågældende kø eller emne.

Alle Service Bus SMS enheder (køer, emner, relæer) er placeret et service navneområde, som er knyttet til et datacenter. Service Bus aktiverer ikke automatisk geografisk-replikering af data og tillader det et navneområde til strækker sig over flere datacentre.

## <a name="protecting-against-acs-outages"></a>Beskyttelse mod ACS udfald

Hvis du bruger ACS legitimationsoplysninger, og ACS bliver utilgængeligt, kan klienter ikke længere få tokens. Klienter, der har et token på det tidspunkt, ACS går ned kan fortsætte med at bruge Service Bus, indtil tokens udløber. Token standardlevetiden er 3 timer.

Brug delt Access signatur (SAS) tokens for at beskytte mod ACS afbrydelser. I dette tilfælde godkender klienten direkte med Service Bus ved at logge af en præsentation minted token med en hemmeligt nøgle. Opkald til ACS er ikke længere påkrævet. Du kan finde flere oplysninger om SAS tokens, [Service Bus godkendelse][].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Beskytte køer og emner mod messaging store fejl

En ikke-opdelt kø eller emne er tildelt til en SMS store. Hvis denne SMS store er tilgængelig, mislykkes alle handlinger på den pågældende kø eller emne. En partitioneret kø på den anden side består af flere fragmenter. Hver fragment er gemt i en anden SMS butik. Når en meddelelse er sendt til en partitioneret kø eller emne, tildeler Service Bus meddelelsen til en af fragmenter. Hvis den tilsvarende SMS store er tilgængelig, skriver Service Bus meddelelsen til en anden fragment, hvis det er muligt. Du kan finde flere oplysninger om partitioneret enheder, se [opdelt SMS enheder][].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Beskyttelse mod datacenter udfald eller nedbrud

Hvis du vil have en failover mellem to datacentre, kan du oprette et Service Bus service navneområde i hver datacenter. For eksempel Service Bus service navneområde **contosoPrimary.servicebus.windows.net** kan være placeret i USA nord/centrale område, og **contosoSecondary.servicebus.windows.net** kan være placeret i os syd/centrale område. Hvis en tjeneste Bus messaging enhed skal forblive handicapvenlige tilstedeværelse af en datacenter afbrydelse, kan du oprette pågældende objekt i begge navneområder.

Du kan finde flere oplysninger i afsnittet "Fejl i forbindelse med Service Bus inden for en Azure datacenter" i [asynkron SMS mønstre og høj tilgængelighed][].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Beskytte relay slutpunkter mod datacenter udfald eller nedbrud

Geografisk replikering af relay slutpunkter gør det muligt for en tjeneste, der viser et relay slutpunkt for at nås tilstedeværelse af Service Bus afbrydelser. For at opnå geografisk gentagelse, oprette tjenesten to relay slutpunkter i forskellige navneområder. Navneområder skal være placeret i forskellige datacentre og de to slutpunkter skal have forskellige navne. En primær slutpunkt kan for eksempel ringes under **contosoPrimary.servicebus.windows.net/myPrimaryService**, mens dens sekundær modstykke kan kontaktes under **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Tjenesten derefter lytter på begge slutpunkter, og en klient kan aktivere tjenesten via et slutpunkt. En klientprogrammet tilfældigt henter en af relæer som det primære slutpunkt og sender sin anmodning til det aktive slutpunkt. Hvis handlingen mislykkes med en fejlkode, angiver fejlen, relay slutpunktet ikke er tilgængelig. Programmet åbnes en kanal at sikkerhedskopiering slutpunktet og gentager anmodningen. På dette tidspunkt aktive og sikkerhedskopiering slutpunkterne skifte roller: klientprogrammet finder det gamle aktive slutpunkt skal være den nye sikkerhedskopiering slutpunkt og den gamle sikkerhedskopiering slutpunkt skal være det nye aktive slutpunkt. Hvis begge sender handlinger fejl, rollerne for de to enheder ændres, og der returneres en fejl.

Eksemplet [geografisk replikering med tjenesten Bus videresendes meddelelser][] demonstrerer, hvordan du overfører relæer.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Beskytte køer og emner mod datacenter udfald eller nedbrud

For at opnå spændstighed mod datacenter udfald, når du bruger formidlet messaging, Service Bus understøtter to metoder: *aktiv* og *passiv* gentagelse. For hver enkelt metode, hvis en given kø eller et emne skal forblive handicapvenlige tilstedeværelse af en datacenter afbrydelse, kan du oprette den i begge navneområder. Begge objekter kan have samme navn. En primær kø kan for eksempel ringes under **contosoPrimary.servicebus.windows.net/myQueue**, mens dens sekundær modstykke kan kontaktes under **contosoSecondary.servicebus.windows.net/myQueue**.

Hvis programmet ikke kræver permanent afsender til modtager kommunikation, kan programmet implementere en robust klientsiden kø til at undgå tab af meddelelse og til at beskytte afsenderen fra midlertidige Service Bus fejl.

## <a name="active-replication"></a>Aktive gentagelse

Aktive gentagelse bruger objekter i begge navneområder for hver operation. En klient, der sender en meddelelse sender to kopier af den samme meddelelse. Den første kopi sendes til det primære objekt (for eksempel **contosoPrimary.servicebus.windows.net/sales**), og den anden kopi af meddelelsen sendes til den sekundære enhed (for eksempel **contosoSecondary.servicebus.windows.net/sales**).

En klient modtager meddelelser fra begge køer. Modtageren behandler den første kopi af en meddelelse, og den anden kopi er deaktiveret. Hvis du vil udelade dublerede meddelelser, skal afsenderen mærke hver meddelelse med et entydigt id. Begge kopier af meddelelsen være mærket med det samme id. Du kan bruge egenskaberne [BrokeredMessage.MessageId][] eller [BrokeredMessage.Label][] eller en brugerdefineret egenskab til at mærke meddelelsen. Modtageren skal vedligeholde en liste over meddelelser, den allerede har modtaget.

[Geografisk replikering med tjenesten Bus formidlet meddelelser][] eksempel demonstrerer aktive replikering af messaging enheder.

> [AZURE.NOTE] Den aktive gentagelse tilgang fordobles antallet af handlinger, og derfor denne metode kan det medføre højere omkostninger.

## <a name="passive-replication"></a>Passive gentagelse

I tilfælde fri for fejl bruger passive gentagelse kun én af de to SMS enheder. En klient sender meddelelsen til den aktive enhed. Hvis handlingen på den aktive enhed mislykkes med en fejlkode, der angiver det datacenter, der er vært den aktive enhed er ikke muligvis tilgængelig, sender klienten en kopi af meddelelsen til den ekstra enhed. På dette tidspunkt aktivt og de ekstra enheder skifte roller: afsendelse klienten finder gamle aktive objektet skal være det nye sikkerhedskopiering objekt, og den gamle sikkerhedskopiering enhed er det nye aktive objekt. Hvis begge sender handlinger fejl, rollerne for de to enheder ændres, og der returneres en fejl.

En klient modtager meddelelser fra begge køer. Da der er mulighed for, at modtageren modtager to kopier af den samme meddelelse, skal modtageren Udelad dublerede meddelelser. Du kan skjule dubletter på samme måde, som der beskrevet under aktive gentagelse.

Passive replikering er generelt mere økonomisk end aktive gentagelse, fordi i de fleste tilfælde kun én handlingen er udført. Ventetid, overførselshastighed og omkostninger er identiske til ikke-replikerede scenariet.

Når du bruger passive gentagelse, i følgende scenarier kan meddelelser mistes eller modtaget to gange:

-   **Meddelelse forsinkelse eller tab**: forudsætter, at afsenderen sendt en meddelelse m1 til den primære kø, og derefter køen bliver utilgængeligt før modtageren modtager m1. Afsenderen sender en efterfølgende meddelelse m2 til sekundær køen. Hvis den primære kø er ikke tilgængelig i øjeblikket, modtager modtageren m1, når køen bliver tilgængelig igen. I tilfælde af nedbrud modtager modtageren muligvis aldrig m1.

-   **Duplikere modtagelse**: forudsætter, at afsenderen sender en meddelelse m til den primære kø. Service Bus behandler m korrekt, men de kan ikke sende et svar. Når handlingen send timeout, sender afsenderen en identisk kopi af m til sekundær køen. Hvis modtageren er at modtage den første kopi af m, før den primære kø bliver utilgængeligt, modtager modtageren begge kopier af m på omtrent samme tid. Hvis modtageren ikke kunne modtage den første kopi af m, før den primære kø bliver utilgængeligt, modtageren modtager indledningsvis kun den anden kopi af m, men derefter modtager en anden kopi af m, når den primære kø bliver tilgængelig.

[Geografisk replikering med tjenesten Bus formidlet meddelelser][] eksempel demonstrerer passive replikering af messaging enheder.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om nedbrud, i følgende artikler:

- [Forretningskontinuitet for Azure SQL-Database][]
- [Azure fleksibilitet teknisk vejledning][]

  [Tjenesten Bus godkendelse]: service-bus-authentication-and-authorization.md
  [Partitioneret SMS enheder]: service-bus-partitioning.md
  [Asynkron SMS mønstre og høj tilgængelighed]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [Geografisk-replikering med Service Bus videresendes meddelelser]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [Geografisk-replikering med Service Bus formidlet meddelelser]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Forretningskontinuitet for Azure SQL-Database]: ../sql-database/sql-database-business-continuity.md
  [Azure fleksibilitet teknisk vejledning]: ../resiliency/resiliency-technical-guidance.md
