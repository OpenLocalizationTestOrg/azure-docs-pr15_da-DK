<properties
    pageTitle="Service Bus SMS oversigt | Microsoft Azure"
    description="Tjenesten Bus Messaging: fleksible datalevering i skyen"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="sethm"/>


# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Service Bus messaging: levering af fleksible data i skyen

Microsoft Azure Service Bus er en tjeneste til levering af pålidelige oplysninger. Formålet med denne tjeneste er at gøre det nemmere at kommunikation. Når to eller flere parter vil udveksle oplysninger, de skal bruge en kommunikation ordning. Tjenesten Bus er en formidlet eller tredjepart kommunikation ordning. Dette er lig med en posttjeneste i den fysiske verden. Posttjenester gør det meget nemt at sende forskellige typer af breve og pakker med en lang række levering garantier et vilkårligt sted i verden.

Svarer til tjenesten post fremvisning af bogstaver, Service Bus er levering af fleksible oplysninger fra både afsenderen og modtageren. Messaging-tjenesten sikrer, at oplysninger, der leveres, selvom de to parter er aldrig både online på samme tid, eller hvis de er ikke tilgængelige på nøjagtigt samme tid. På denne måde er meddelelser svarer til at sende et bogstav, mens ikke-formidlet kommunikation ligner placere et telefonopkald (eller hvordan et telefonopkald bruges til at være - før opkald vente og opkalds-ID, som er meget mere som formidlet messaging).

Meddelelsens afsender kan også kræver en række forskellige levering egenskaber, herunder transaktioner, dubletter, tidsbaserede udløb og samling. Disse mønstre har postnummer Analogier samt: Gentag levering, kræves signatur, Skift adresse eller tilbagekaldelsen.

Service Bus understøtter to forskellige SMS mønstre: *Relay* og *formidlet messaging*.

## <a name="service-bus-relay"></a>Tjenesten Bus Relay

[Relay](../service-bus-relay/service-bus-relay-overview.md) -komponenten af Service Bus er et centralt (men meget netværksbelastningen) tjeneste, der understøtter en lang række forskellige transportprotokoller og Web services standarder. Dette omfatter SOAP WS-, *, og endda RESTEN. [Relay-tjenesten](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) giver en række forskellige relay connectivity indstillinger og kan hjælpe forhandl direkte peer-to-peer-forbindelser, når det er muligt. Service Bus er optimeret til .NET udviklere, som bruger Windows Communication Foundation (WCF), begge med hensyn til ydeevne og brugervenligheden, og giver fuld adgang til dens relay-tjenesten via SOAP og RESTEN grænseflader. Dette gør det muligt for en hvilken som helst SOAP eller RESTEN programming miljø for at integrere med Service Bus.

Relay-tjenesten understøtter traditionelle envejs messaging, anmodning/svar messaging og peer-to-peer-beskeder. Understøtter også begivenhed fordeling på Internet-omfang for at aktivere udgivelse abonnement scenarier og tovejs-socket kommunikation til øget punkt til punkt effektivitet. I det relayed SMS mønster en lokal tjeneste opretter forbindelse til relay-tjenesten via en udgående port og opretter en tovejs-socket til kommunikation, der er bundet til en bestemt rendezvous adresse. Klienten kan derefter kommunikere med tjenesten lokalt ved at sende meddelelser til relay-tjenesten målretning af rendezvous adresse. Relay-tjenesten overføre"" derefter meddelelser til lokale tjenesten via tovejs-socket allerede er på plads. Klienten ikke skal bruge en direkte forbindelse til tjenesten i det lokale miljø, og ikke nødvendigvis kræves det, hvor tjenesten er placeret, og den lokale tjeneste behøver ikke nogen indgående porte åbne firewallen.

Du starter forbindelsen mellem din lokale tjeneste og relay-tjenesten ved hjælp af en pakke med WCF "relay" bindinger. I baggrunden Knyt relay bindinger transport binding elementer, der er udviklet til at oprette WCF kanal komponenter, der integreres med Service Bus i skyen.

Tjenesten Bus Relay indeholder mange fordele, men kræver serveren og klienten både være online ad gangen for at kunne sende og modtage meddelelser. Dette er ikke optimal til HTTP-typografi kommunikation, hvori anmodningerne ikke måske er typisk langlivet, eller for klienter, der forbinder kun nogle gange kan f.eks browsere, mobilprogrammer, og så videre. Formidlet messaging understøtter Afkoblet kommunikation og har sine fordele klienter og servere kan oprette forbindelse, når det er nødvendigt, og udføre deres handlinger i en asynkron måde.

## <a name="brokered-messaging"></a>Formidlet messaging

I modsætning til farveskema relay kan [formidlet messaging](service-bus-queues-topics-subscriptions.md) sammenligne som asynkron eller "temporally frakoblet". Producenter (afsendere) og forbrugere (modtagere) behøver ikke at være online på samme tid. Messaging-infrastrukturen gemmer pålideligt meddelelser i en "mægler" (som en kø), indtil arket personen er klar til at modtage dem. Dette giver mulighed for komponenterne i den distribuerede programmer til at blive afbrudt, enten frivilligt; for eksempel til vedligeholdelse, eller på grund af nedbrud komponent, uden påvirker hele systemet. Desuden må programmet modtagelse kun have kommer online i visse perioder for dag, som et lager-system, der kun er påkrævet til at køre i slutningen af arbejdsdag.

Vigtigste komponenter af Service Bus formidlet SMS-infrastrukturen er køer, emner og abonnementer.  Den primære forskel er, at emner understøtter udgivelse/abonnement funktioner, der kan bruges til avanceret indhold-baserede routing og levering logik, herunder afsendelse til flere modtagere. Disse komponenter aktivere nye asynkron SMS scenarier, som tidsmæssig afkobling, udgivelse/abonnement og justering af belastning. Se [Service Bus køer, emner og abonnementer](service-bus-queues-topics-subscriptions.md)kan finde flere oplysninger om disse SMS enheder.

Som med infrastruktur Relay er formidlet SMS muligheden angivet til WCF- og .NET Framework programmører og også via RESTEN.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Service Bus messaging, under følgende emner.

- [Service Bus grundlæggende](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus køer, emner og abonnementer](service-bus-queues-topics-subscriptions.md)
- [Sådan bruger du Service Bus køer](service-bus-dotnet-get-started-with-queues.md)
- [Sådan bruges Service Bus emner og abonnementer](./service-bus-dotnet-how-to-use-topics-subscriptions.md)
 
