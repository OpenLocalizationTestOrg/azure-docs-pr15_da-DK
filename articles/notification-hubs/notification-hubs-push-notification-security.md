<properties
    pageTitle="Sikkerhed i forbindelse med besked om Hubs"
    description="Dette emne forklares sikkerhed i forbindelse med Azure meddelelse hubs."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>Sikkerhed

##<a name="overview"></a>Oversigt

Dette emne beskrives sikkerhedsmodel af Azure meddelelse Hubs. Da meddelelse Hubs er en Service Bus enhed, de har implementeret den samme sikkerhedsmodel som Service Bus. Du kan finde yderligere oplysninger finder [Service Bus godkendelse](https://msdn.microsoft.com/library/azure/dn155925.aspx) emner.

##<a name="shared-access-signature-security-sas"></a>Delt adgang signatur sikkerhed (SAS) 

Meddelelse om Hubs implementerer et objekt-sikkerhed på brugerniveau farveskema hedder SAS (delt Access signatur). Denne ordning gør det muligt for SMS enheder erklære op til 12-reglerne i deres beskrivelse, som giver rettigheder til objektet.

Alle regler indeholder et navn, en nøgleværdi (delte hemmeligt) og et sæt rettigheder, som beskrevet i afsnittet "Sikkerhed krav." Når du opretter en meddelelse om-Hub, to regler oprettes automatisk: én med lytte rettigheder (der bruger appen klienten) og én med alle rettigheder (der bruger app backend-version).

Når du udfører registrering Administration fra klient apps, hvis oplysninger, der sendes via meddelelser er ikke følsomme (for eksempel, vejrudsigt opdateringer), et almindelige metode til at få adgang til en meddelelse om Hub er at give den nøgleværdi for reglen Listen-only adgang til appen klienten på computeren, og at give den nøgleværdi for den regel fuld adgang til app backend-version.

Det anbefales ikke, at du integrere den nøgleværdi i Windows Store-klienten apps. En metode til at undgå at integrere den nøgleværdi er at hente det fra app backend-version ved start klient-appen.

Det er vigtigt at forstå, tasten med lytte access tillader en klient-app til at tilmelde dig noget mærke. Hvis din app skal begrænse registreringer til bestemte koder til bestemte klienter (for eksempel, når mærker repræsenterer bruger-id'er), skal din app back-end udføre registreringer. Yderligere oplysninger finder du i registrering i administration. Bemærk, at på denne måde, klient-app ikke vil har direkte adgang til meddelelse hubber.

##<a name="security-claims"></a>Sikkerhed krav

Svarer til andre enheder, meddelelse om Hub handlinger er tilladt for tre sikkerhed krav: lytte, sende og administrere.

| Gør krav | Beskrivelse | Som er tilladt |
|-------|-------------|--------------------|
| Lytte | Oprette eller opdatere, læse og slette enkelt registreringer | Oprette eller opdatere registrering<br><br>Læs registrering<br><br>Læs alle registreringer for et håndtag<br><br>Slette registrering |
| Send | Sende meddelelser til hubben meddelelse | Sende meddelelse |
| Administrere | CRUDs på meddelelse Hubs (herunder opdatering af PNS legitimationsoplysninger og sikkerhedsnøgler), og Læs registreringer, der er baseret på mærker | Opret/Opdater/læst/Slet meddelelse hubs<br><br>Læs registreringer efter mærke |


Meddelelse om Hubs Acceptér krav, der er tildelt Microsoft Azure adgangskontrol tokens og signatur tokens genereres med delte nøgler konfigureret direkte på hubben meddelelse.