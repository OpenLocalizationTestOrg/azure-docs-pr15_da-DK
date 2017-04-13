<properties 
    pageTitle="Service Bus arkitektur | Microsoft Azure"
    description="Beskriver de meddelelse og relay behandling arkitekturen i Azure Service Bus."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm" />

# <a name="service-bus-architecture"></a>Service Bus arkitektur

I denne artikel beskrives meddelelse og relay behandling arkitekturen i Azure Service Bus.

## <a name="service-bus-scale-units"></a>Service Bus skalaenheder

Service Bus er organiseret efter *skalaenheder*. En enhed for tidsskala er en enhed for installation og indeholder alle komponenter, der kræves køre tjenesten. Hvert område installerer en eller flere Service Bus skalaenheder.

Et Service Bus navneområde er knyttet til en enhed for tidsskala. Enhed for tidsskala håndterer alle typer Service Bus enheder: relæer og formidlet SMS enheder (køer, emner, abonnementer). En enhed for tidsskala Service Bus består af følgende komponenter:

- **Et sæt af gatewayen knuder.** Gatewayen noder godkende indgående anmodninger og håndtere anmodninger om relay. Hver gateway node har en offentlig IP-adresse.

- **Et sæt af messaging broker noder.** SMS broker noder behandle anmodninger vedrørende SMS enheder.

- **Én gateway store.** Gateway-store indeholder dataene for hver enhed, der er defineret i denne enhed for tidsskala. Gateway-store er implementeret oven på en SQL Azure-database.

- **Flere SMS butikker.** SMS butikker hold meddelelserne i alle køer, emner og abonnementer, der er defineret i denne enhed for tidsskala. Den indeholder også alle dataene på abonnement. Medmindre [partitioneret messaging objekter](service-bus-partitioning.md) er aktiveret, er en kø eller emne knyttet til en SMS store. Abonnementer er gemt i den samme SMS store som deres overordnede emne. Med undtagelse af Service Bus [Premium Messaging](service-bus-premium-messaging.md)implementeres SMS butikker oven på SQL Azure-databaser.

## <a name="containers"></a>Objektbeholdere

Hver SMS enhed er tildelt en bestemt objektbeholder. En objektbeholder er en logisk konstruktion, der bruger ét messaging butik til butik alle relevante data for denne beholder. Hver beholder er tildelt til en SMS broker node. Typisk, der er flere beholdere end messaging broker noder. Derfor indlæser hver SMS broker node flere beholdere. Fordelingen af objektbeholdere til en SMS broker node er organiseret, så alle SMS broker noder indlæses lige. Hvis afkrydsningsfeltet Indlæs mønster ændringer (for eksempel en af de beholdere henter meget optaget), eller hvis en SMS broker node bliver ikke tilgængelig i øjeblikket, er beholderne fordeles mellem noderne SMS broker.

## <a name="processing-of-incoming-messaging-requests"></a>Behandling af anmodninger om indgående beskeder

Når en klient sender en anmodning til Service Bus, dirigerer Azure belastning den til en af noderne gateway. Noden gateway godkender anmodningen. Hvis anmodningen vedrører en SMS-enhed (kø, emne, abonnement), søger efter objektet i gateway store noden gateway, og bestemmer, hvilke SMS store objektet er placeret. Det søger derefter efter hvilke SMS broker node behandler denne objektbeholder og sender anmodningen til denne SMS broker node. Noden SMS broker behandler anmodningen og opdaterer tilstanden objekt i beholderen store. Noden SMS broker derefter sender svar tilbage til noden gateway, som sender et passende svar tilbage til den klient, der udstedt den oprindelige anmodning.

![Behandling af anmodninger om indgående beskeder](./media/service-bus-architecture/IC690644.png)

## <a name="processing-of-incoming-relay-requests"></a>Behandling af anmodninger om indgående relay

Når en klient sender en anmodning til Service Bus, dirigerer Azure belastning den til en af noderne gateway. Hvis anmodningen er en anmodning om lytte, opretter en ny relay noden gateway. Hvis anmodningen er en anmodning om forbindelse til en bestemt relay, videresender noden gateway anmodning om forbindelse til noden gateway, der ejer videresendelse. Noden gateway, der ejer videresendelse sender en anmodning om rendezvous til lytte klienten, hvor du bliver spurgt lytteren at oprette en midlertidig kanal til noden gateway, modtaget anmodning om forbindelse.

Når relay forbindelsen er oprettet, kan klienterne udveksle meddelelser via noden gateway, der bruges til rendezvous.

![Behandling af anmodninger om indgående Relay](./media/service-bus-architecture/IC690645.png)

## <a name="next-steps"></a>Næste trin

Nu hvor du har læst Besøg følgende links i en oversigt over Service Bus arkitekturen, at komme i gang:

- [Service Bus SMS oversigt](service-bus-messaging-overview.md)
- [Service Bus grundlæggende](service-bus-fundamentals-hybrid-solutions.md)
- [En i kø SMS løsning ved hjælp af Service Bus køer](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
