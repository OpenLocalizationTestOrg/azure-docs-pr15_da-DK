<properties
    pageTitle="Oversigt over Service Bus relay | Microsoft Azure"
    description="Oversigt over Service Bus relay."
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
    ms.date="09/01/2016"
    ms.author="sethm"/>


# <a name="overview-of-service-bus-relay"></a>Oversigt over Service Bus relay

En større komponent af Service Bus er et centralt (men meget netværksbelastningen) *relay* -tjenesten, der gør det muligt at bygge hybrid programmer, der kører i både en Azure datacenter og din egen lokale virksomhedsmiljø.  Service Bus relay understøtter en række forskellige transportprotokoller og web services standarder. Dette omfatter SOAP WS-, *, og endda RESTEN. Relay-tjenesten letter programmerne hybrid ved at gøre det muligt at vise sikkert Windows Communication Foundation (WCF) tjenester, der findes i et virksomhedsmiljø virksomhedens netværk i offentlige skyen, uden at åbne en firewallforbindelse eller kræver forstyrrende ændringer i en virksomhedens netværksinfrastruktur. 

![Relay begreber](./media/service-bus-relay-overview/sb-relay-01.png)

Relay-tjenesten understøtter traditionelle envejs messaging, anmodning/svar messaging og peer-to-peer-beskeder. Det understøtter også begivenhed fordeling på internet-omfang for at aktivere udgivelse/abonnement scenarier og tovejs-socket kommunikation til øget punkt til punkt effektivitet. 

I det relayed SMS mønster en lokal tjeneste opretter forbindelse til relay-tjenesten via en udgående port og opretter en tovejs-socket til kommunikation, der er bundet til en bestemt rendezvous adresse. Klienten kan derefter kommunikere med tjenesten lokalt ved at sende meddelelser til relay-tjenesten målretning af rendezvous adresse. Relay-tjenesten overføre"" derefter meddelelser til lokale tjenesten via tovejs-socket allerede er på plads. Klienten ikke skal bruge en direkte forbindelse til tjenesten lokale skal det ikke er nødvendig for, hvor tjenesten er placeret, og den lokale tjeneste behøver ikke nogen indgående porte åbne firewallen.

Du kan starte forbindelsen mellem din lokalt tjeneste og relay-tjenesten ved hjælp af en pakke med WCF "relay" bindinger. I baggrunden Knyt relay bindinger til nye transport binding elementer udviklet til at oprette WCF kanal komponenter, der integreres med Service Bus i skyen. 

## <a name="next-steps"></a>Næste trin

Få mere at vide om Service Bus relay under følgende emner.

- [Azure Service Bus arkitektonisk oversigt](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Sådan bruger du Service Bus Relay-tjenesten](service-bus-dotnet-how-to-use-relay.md)

 