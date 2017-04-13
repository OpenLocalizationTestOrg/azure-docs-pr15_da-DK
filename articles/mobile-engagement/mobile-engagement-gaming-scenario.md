<properties 
    pageTitle="Azure Mobile aftale implementering for Gaming App"
    description="Gaming app scenarie at implementere Azure Mobile aftale" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

#<a name="implement-mobile-engagement-with-gaming-app"></a>Implementere Mobile aftale med Gaming App

## <a name="overview"></a>Oversigt

En gaming opstart har startet en ny fiskeri baseret role-play/strategi game app. Game har været oppe at køre i 6 måneder. Denne game er en stor succes, og den har millioner af overførsler og tilbageholdelse er meget høj sammenlignet med andre opstart game apps. På mødet kvartalsvis Gennemse accepterer interessenter, de har brug at øge gennemsnitlig omsætning per bruger (ARPU). Premium i game pakker er tilgængelige som særlige tilbud. Disse game packs Tillad brugere at opgradere udseende og ydelse af deres fiskeri linjer og lures eller tackles i game. Pakke salg er dog meget lav. Så de beslutter først at analysere kundeoplevelsen med et værktøj til analyse og program at øge salg ved hjælp af avancerede derefter for at udvikle en aftale segmentering.

Baseret på [Azure Mobile aftale - Introduktion med bedste fremgangsmåder](mobile-engagement-getting-started-best-practices.md) , de bygger strategi for en aftale.

##<a name="objectives-and-kpis"></a>Mål og KPI'er

Vigtige interessenter for game møde. Alle aftaler én Hovedformålet - at øge premium pakke salg med 15%. De opretter Business Key Performance Indicators (KPI'er) mål og drev dette mål

* Niveau af game pakkerne købes?
* Hvad er indtægter per bruger, hver enkelt session, om ugen og månedsbasis?
* Hvad er de forskellige typer foretrukne køb?

Del 1 i [Introduktion til](mobile-engagement-getting-started-best-practices.md) forklares, hvordan du definerer mål og KPI'er. 

Med de Business KPI'er nu defineret, opretter Product Manager Mobile aftale KPI'er for at afgøre ny bruger tendenser og opbevaring.

* Overvåge opbevaring og brug på tværs af de følgende intervaller: dagligt, hver 2 dage, ugentligt, månedligt og hver 3 måneder
* Den aktive bruger tæller
* App bedømmelse i store

Baseret på anbefalinger fra IT-teamet, er de følgende tekniske KPI'er føjet til at besvare spørgsmål, der er følgende:

* Hvad er mit bruger-stien (besøg på hvilken side, hvor meget tid brugere bruger på det)
* Antallet af nedbrud eller fejl, der er stødt på hver enkelt session
* Hvilke versioner af Operativsystemet kører Mine brugere?
* Hvad er den gennemsnitlige størrelse på skærmen til Mine brugere?
* Hvilken type forbindelse til internettet har Mine brugere?

For hver KPI angiver Mobile Product Manager de data, hun har brug for, og hvor den er placeret i sin playbook.

## <a name="engagement-program-and-integration"></a>Aftale program og integration

Før du kan bygge et avanceret aftale-program, skal den Mobile Project direktør ansvarlig for projektet har en deep forståelse af, hvordan, og hvor produkter, der bruges af brugerne.

Den Mobile Project direktør har indsamlet nok data for at forbedre sin i app'en opslagsnål meddelelse salg efter 3 måneder. Han lærer, som:

* Det første køb sker generelt på niveauet for 14. For 90% af disse tilfælde er købet nye legendariske våben til $3.
* Brugere, der har foretaget et køb, Fortsæt med produktet, og Opret flere køber i 80% af disse tilfælde.
* Brugere, der er gået niveauet 20, begynde at bruge mere end $10/ uge.
* Brugere har tendens til at købe premium-pakker på niveau 16, 24 og 32.

Den Mobile Project direktør beslutter sammen takket være denne analyse til at oprette bestemte opslagsnål meddelelse serier at øge i app salg. Hun opretter tre opslagsnål serier som han kalder: Velkommen program, salg Program og inaktiv Program. Du kan finde flere oplysninger, der referere til [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
