<properties
    pageTitle="Hvad er Azure relay? | Microsoft Azure"
    description="Oversigt over Azure Relay"
    services="service-bus"
    documentationCenter=".net"
    authors="banisadr"
    manager="timlt"
    editor="" />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="babanisa" />

# <a name="what-is-azure-relay"></a>Hvad er Azure Relay?

Azure Relay-tjenesten letter programmerne hybrid ved at gøre det muligt at vise sikkert tjenester, der findes i et virksomhedsmiljø virksomhedens netværk i offentlige skyen, uden at åbne en firewallforbindelse eller kræver forstyrrende ændringer i en virksomhedens netværksinfrastruktur. Relay understøtter en række forskellige transportprotokoller og web services standarder.

Relay-tjenesten understøtter traditionelle til envejs, anmodning om svar og peer-to-peer-trafik. Det understøtter også begivenhed fordeling på internet-omfang for at aktivere udgivelse/abonnement scenarier og tovejs-socket kommunikation til øget punkt til punkt effektivitet. 

I et mønster videresendes data Overfør en lokal tjeneste opretter forbindelse til relay-tjenesten via en udgående port og opretter en tovejs-socket til kommunikation, der er bundet til en bestemt rendezvous adresse. Klienten kan derefter kommunikere med tjenesten lokalt ved at sende trafik til relay-tjenesten målretning af rendezvous adresse. Relay-tjenesten overføre"" derefter data til lokale tjenesten via et tovejs-socket dedikeret til hver klient. Klienten ikke skal bruge en direkte forbindelse til tjenesten lokale skal det ikke er nødvendig for, hvor tjenesten er placeret, og den lokale tjeneste behøver ikke nogen indgående porte åbne firewallen.

De vigtigste egenskab elementer, der leveres af Relay er tovejs-, uden buffer kommunikation på tværs af netværk med TCP-lignende (throttling), slutpunkt registrering, connectivity status, og lagt over endpoint-sikkerhed. Relays funktioner adskiller sig fra netværk niveau integration teknologier som VPN i, at det kan være fastsat til et enkelt program slutpunkt på en enkelt maskine, mens VPN-teknologi er langt mere forstyrrende, som den er afhængig af at ændre netværksmiljøet.

Azure Relay har to funktioner:

1. [Hybrid forbindelser](#hybrid-connections) - bruger den åbne standard Web Sockets aktivere flere platforme scenarier

2. [WCF relæer](#wcf-relays) - bruger Windows Communication Foundation til at aktivere ekstern beskrivelse af opkald

Hybrid forbindelser og WCF relæer aktivere sikker forbindelse til komponenter, der findes i et virksomhedsmiljø virksomhedens netværk. Brug af ligger oven på hinanden er afhængig af dine specifikke behov, der er beskrevet nedenfor:

|                                    | WCF Relay | Hybrid forbindelser |
| ---------------------------------- |:---------:|:------------------:|
| **WCF**                            |     x     |                    |
| **.NET core**                      |           |         x          |
| **.NET framework**                 |     x     |         x          |
| **JavaScript/NodeJS**              |           |         x          |
| **Java***                          |           |         x          |
| **Baseret på standarder Open Protocol**  |           |         x          |
| **Flere RPC Programing modeller** |           |         x          |
*<sub>Efter generelle tilgængelighed</sub>

## <a name="hybrid-connections"></a>Hybrid forbindelser

Azure Relay Hybrid-forbindelser, der ikke er en sikker, Åbn protocol udviklingen af de eksisterende Relay-funktioner, der kan være implementeret på en hvilken som helst platform og på alle sprog, der indeholder en grundlæggende WebSocket egenskab, som omfatter eksplicit WebSocket API fælles webbrowsere. Hybrid forbindelser er baseret på http- og WebSockets.

## <a name="wcf-relays"></a>WCF relæer

WCF videresendelse fungerer for fuld .NET Framework (NETFX) og WCF. Du kan starte forbindelsen mellem din lokalt tjeneste og relay-tjenesten ved hjælp af en pakke med WCF "relay" bindinger. I baggrunden Knyt relay bindinger til nye transport binding elementer udviklet til at oprette WCF kanal komponenter, der integreres med Service Bus i skyen.

## <a name="service-history"></a>Oversigt over Service

Hybrid forbindelser rodskårne den tidligere, lige med navnet "BizTalk-tjenester" funktion, der er bygget på Azure Service Bus WCF Relay. Den nye funktion i hybride forbindelser supplerer den eksisterende WCF-Relay og funktioner i disse to tjenester findes side om side i Relay-tjenesten for nær fremtid; de dele en fælles gateway, men er ellers forskellige installationer.

WCF Relay er ældre videresendelse med tilbud om at mange kunder allerede kan bruge med deres WCF programing modeller.

## <a name="next-steps"></a>Næste trin:

- [Relay ofte stillede spørgsmål](relay-faq.md)
- [Oprette et navneområde](relay-create-namespace-portal.md)
- [Introduktion til .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Komme i gang med Node](relay-hybrid-connections-node-get-started.md)