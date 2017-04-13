<properties 
    pageTitle="Azure Mobile aftale implementering for medier App"
    description="Medier app scenarie at implementere Azure Mobile aftale" 
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

#<a name="implement-mobile-engagement-with-media-app"></a>Implementere Mobile aftale med medier App

## <a name="overview"></a>Oversigt

John er mobile projektleder for en stor medier virksomhed. Han senest startes en ny app, der indeholder en meget høj download optælling. Hun har påløber sin mål til hentning, men stadig sin returnere på Investment(ROI) per bruger opfylder ikke sit behov. 

John har allerede identificeret, hvorfor sin Investering er for lav. Brugere stoppe ofte med at bruge sin app efter kun 2 uger, og de fleste af dem vender aldrig tilbage. Han vil øge opbevaring af sin app.

Når nogle første test, salgsrepræsentant hun har lært, når han deltager sin brugere med pushmeddelelser, for at fortsætte med at bruge sin app. Brugere, der er inaktive returnerer også ofte til appen afhængigt af meddelelser, som hun får tilsendt. John beslutter at investere i en slags aftale Program til sin som anvender Avanceret målretning af med pushmeddelelser.

John har senest læst [Azure Mobile aftale - Introduktion med bedste fremgangsmåder](mobile-engagement-getting-started-best-practices.md) og har besluttet at implementere anbefalinger fra guiden.

##<a name="objectives-and-kpis"></a>Mål og KPI'er

Vigtige interessenter for Johns app opfylder. Business er dannet ud fra Active Directory, som brugerne bruge sin medier. Ved at øge indhold consumed per bruger, øges John sin indtægter. Alle blive enige om én Hovedformålet: til at øge salget fra reklamer med 25%. De opretter Business Key Performance Indicators (KPI'er) mål og drev dette mål

* Antallet af reklamer har klikket på per bruger
* Hvor mange artikelsider besøgte (per bruger / hver enkelt session / om ugen / månedsbasis...)
* Hvad er foretrukne kategorier

Baseret på Johns møde med vigtige interessenter har hun defineret sin Business KPI'er. Hun følger del 1 i [Azure Mobile aftale - Introduktion med bedste fremgangsmåder](mobile-engagement-getting-started-best-practices.md). 

Nu skal opretter hun de følgende aftale KPI'er for at sikre, at mål er nået:

* Overvåge opbevaring på tværs af de følgende intervaller: daglig, uge, bi-hver uge eller måned.
* Aktive brugere tæller
* App rangering i appen gemmer

Baseret på anbefalinger fra IT-teamet, er de følgende tekniske KPI'er føjet til at besvare spørgsmål, der er følgende:

* Hvad er mit bruger-stien (besøg på hvilken side, hvor mange tid brugere bruger på det)
* Antallet af nedbrud eller fejl, der er stødt på hver enkelt session?
* Hvilke versioner af Operativsystemet kører Mine brugere?
* Hvad er den gennemsnitlige størrelse på skærmen til Mine brugere?
* Hvilken slags internetforbindelser har Mine brugere?

Han klassificerer de påkrævede data for hver KPI og han poster i den korrekte placering af sin playbook.

## <a name="engagement-program-and-integration"></a>Aftale program og integration

Nu, John er færdig med at definere sin KPI'er, starter han sin aftale strategi fase ved at definere 4 aftale programmer og deres mål:    ![][1]

John går derefter dybere ved der beskriver pushmeddelelser for hvert program. Push-meddelelse, der defineres af fem elementer:

1. Mål: Hvad er formålet med meddelelsen
2. Hvordan formålet ringes
3. Mål: der skal modtage meddelelsen?
4. Content: Hvad er teksten og formatet for meddelelsen (i App/Out af App)
5. Hvornår: Hvad er det bedste tidspunkt at sende meddelelsen opslagsnål

    ![][2]

Referere til [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)kan finde flere oplysninger.

Efter del 2 af hvidbog John bruger target sektion til at angive, hvilke han har til at indsamle data og skriver sin mærke planlægge Fællesskab med IT-team til at implementere løsningen. Efter 1 uge implementering og test af brugeren accept Start John til sidst sin programmer.

##<a name="program-results"></a>Program resultater

4 måneder senere gennemser John ydeevne af programmer. Velkommen til programmet og ugentlig programmet møde sin mål. Antallet af brugere med kun én session reduceres, der bruges flere funktioner i appen og antallet af forbindelser om ugen har dobbelt.

**Inaktiv Program** hjælper John forstå bruger tendenser. Det vises, 15% af inaktive brugere vende tilbage til appen. Men de fleste af dem ikke Oprethold aktive mere end 1 måned. John forventer en potentiel optimering af denne fremgangsmåde med flere meddelelser og udvide sin indhold valgmuligheder.

**Opdag Program** fungerer ikke godt. Den øger på tværs af salg, men ikke nok til at nå sin målsætninger. John identificerer, han ikke har nok data til at foretage relevante målretning og foreslå relevante indhold. Han stopper dette program og fokuserer på at sende "redaktionel pushmeddelelser" med Azure Mobile aftale. Sin journalister har allerede en CMS løsning at sende pushmeddelelser og ønsker ikke at ændre.

John beslutter at bruge API når der er en HTTP REST-API, der gør det muligt for administration af rækkevidde kampagner uden at bruge brugergrænsefladen AZME internettet. Med denne metode kan John indsamle data han skal og Tillad, at sin forfattere at fortsætte med at bruge løsningen CMS.

Hvis du vil kontrollere, at denne funktion virker korrekt, beder John IT-teamet skal være opmærksom på følgende punkter:

1. **Handlingen systemer** : de alle har deres egne regler til at administrere eksterne pushmeddelelser, så John beslutter sig at få vist alle tilfælde og kontrollerer, hvis API'erne håndtere den.
F.eks.: Android opslagsnål system kan overblik, som ikke er tilfældet med iOS.

2. **Tidsramme**: John ønsker et API, som angive tidsrammen og angive en afslutning til kampagner. Hun ønsker at bevare brugere fra en hvilken som helst forstyrrende meddelelse bombing.

3. **Kategorier**: Marketing team Forbereder skabelon for hver type advarer. John beder IT-team til at angive kategorier i API.

Når du har nogle test er John tilfreds. Sammen takket være denne API kan journalister stadig sende pushmeddelelser med deres CMS og Azure Mobile aftale indsamler alle funktionsmæssige data for dem.

Når du har disse 4 først måneder, resultater afspejler en god overordnede ydeevne og giver konfidensinterval for John og sin tavle, Investering per bruger øges per 15% og mobile salget udgør 17.5% af det samlede salg, et større 7.5% kun fire måneder.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
