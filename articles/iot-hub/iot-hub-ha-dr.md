<properties
 pageTitle="IoT Hub HA og DR | Microsoft Azure"
 description="I denne artikel beskrives de funktioner, der hjælper med at opbygge meget tilgængelige IoT løsninger med genoprettelse efter nedbrud."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub høj tilgængelighed og genoprettelse efter nedbrud

Som en Azure service indeholder IoT Hub høj tilgængelighed (HA) ved hjælp af redundans på områdeniveauet Azure uden yderligere arbejde kræves af løsningen. Desuden tilbyder Azure en række funktioner, der hjælper til at oprette løsninger med muligheder for genoprettelse efter genoprettelse (DR) eller kryds-område tilgængelighed, hvis det er nødvendigt. Du skal designe og forberede dine løsninger for at kunne udnytte disse DR funktioner, hvis du vil angive globale, kryds-område høj tilgængelighed for enheder eller brugere. [Azure Business løbende teknisk vejledning](../resiliency/resiliency-technical-guidance.md) i artiklen beskrives de indbyggede funktioner i Azure til Forretningskontinuitet og DR. Papiret [nedbrud og høj tilgængelighed til Azure programmer][] giver arkitektur vejledning i strategier til Azure-programmer for at opnå HA og DR.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Ud over handel område HA implementerer IoT Hub failover metode til nedbrud, der kræver ingen brugerinput fra brugeren. IoT Hub DR startes præsentation og har et gendannelse tid mål (RTO) af 2-26 timer og følgende gendannelse punkt mål (frigivne produktionsordrer).

| Funktionalitet | FRIGIVNE PRODUKTIONSORDRE |
| ------------- | --- |
| Tjenesten kører for registreringsdatabasen og kommunikation handlinger | Mulige CName tab |
| Identitetsdata i enhed identitet registreringsdatabasen | 0-5 minutter tab af data |
| Enhed i skyen meddelelser | Alle ulæste meddelelser går tabt |
| Handlinger overvågning meddelelser | Alle ulæste meddelelser går tabt |
| Cloud-til-enhed meddelelser | 0-5 minutter tab af data |
| Cloud-til-enhed feedback kø | Alle ulæste meddelelser går tabt |

## <a name="regional-failover-with-iot-hub"></a>Internationale failover med IoT Hub

En fuldstændig behandling af installation topologier i IoT løsninger er omfattet af denne artikel, men med henblik på høj tilgængelighed og nedbrud vi vil overveje implementeringsmodel *internationale failover* .

Løsning back-end kører primært i et datacenter placering i en model med internationale failover, men en ekstra IoT hub og back-end er installeret på en anden datacenter placering til failover formål, i tilfælde af IoT hubben i det primære datacenter befinder sig en afbrydelse eller netværksforbindelsen fra enheden til det primære datacenter afbrydes en eller anden måde. Enheder bruge en sekundær tjenesteslutpunkt, når den primære gateway ikke kan kontaktes. Med en egenskab i tværs område failover, kan løsning tilgængeligheden forbedres ud over høj tilgængeligheden af et enkelt område.

På et højt niveau for at implementere en internationale failover modellen med IoT-Hub, skal du følgende.

* **En sekundær IoT hub og enhed routing logik**: tale om en tjenesteforstyrrelse i din primære område enheder skal starte, oprette forbindelse til din sekundære område. Da de fleste tjenester involveret tilstand-aware art, er almindelige for løsning administratorer at udløse mellem område failover-processen. Den bedste måde at kommunikere det nye slutpunkt til enheder, og samtidig bevare kontrollen over processen, er har dem regelmæssigt kontrollere en *concierge* tjeneste for det aktuelle aktive slutpunkt. Tjenesten concierge kan være et enkelt webprogram, der er replikeres og bevares nås ved hjælp af DNS-omdirigering teknikker (for eksempel, ved hjælp af [Azure trafik Manager][]).
* **Identitet registreringsdatabasen gentagelse** - for at kunne bruges, den sekundære IoT hub skal indeholde alle enhed identiteter, der kan oprette forbindelse til løsningen. Løsningen skal opbevare geografisk replikerede sikkerhedskopier af enhed identiteter og overføre dem til den sekundære IoT-hub, inden du skifter det aktive slutpunkt for enhederne. Funktionen enhed identitet eksport af IoT Hub er meget nyttig i denne kontekst. Du kan finde yderligere oplysninger finder [IoT Hub Developer Guide - identitet registreringsdatabasen][].
* **Fletning af logik** – når det primære område bliver tilgængelig igen, alle tilstand og data, der er oprettet i det sekundære websted skal overføres tilbage til det primære område. Dette vedrører hovedsageligt enhed identiteter og program-metadata, der skal flettes med den primære IoT hub og andre programmer-specifikke butikker i det primære område. For at forenkle dette trin, anbefales som regel det, at du bruger idempotent handlinger. Derved minimeres side-effekter ikke kun fra endelig ensartet distribution af hændelser, men også fra dubletter eller ud af rækkefølge leveringen af begivenheder. Desuden bør programmet logik være designet til at acceptere potentielle uoverensstemmelser eller "lidt" af dato-tilstand. Dette er på grund af den ekstra tid det tager for systemet "reparere" baseret på gendannelse punkt målsætninger (frigivne Produktionsordre).

## <a name="next-steps"></a>Næste trin

Følg disse links for at få flere oplysninger om Azure IoT Hub:

- [Komme i gang med IoT Hubs (selvstudium)][lnk-get-started]
- [Hvad er Azure IoT Hub?][]

[Nedbrud og høj tilgængelighed til Azure-programmer]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure trafik Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub Developer Guide - identitet registreringsdatabasen]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Hvad er Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
