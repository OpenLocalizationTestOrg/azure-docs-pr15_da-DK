<properties
    pageTitle="Autoskalering og App Service miljø | Microsoft Azure"
    description="Autoskalering og App Service-miljø"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"
/>

# <a name="autoscaling-and-app-service-environment"></a>Autoskalering og App Service-miljø

Azure App Service-miljøer, der understøtter *Autoskalering*. Du kan Autoskalering enkelt medarbejder grupper baseret på målepunkter eller tidsplan.

![Autoskalering indstillinger for en kollega puljen.][intro]

Autoskalering optimerer din ressource anvendelsen ved automatisk vækst og formindskelse miljøet App Service for at passe til dit budget og eller indlæse profil.

## <a name="configure-worker-pool-autoscale"></a>Konfigurere arbejder puljen Autoskalering

Du kan få adgang til funktionen Autoskalering fra fanen **Indstillinger** i gruppen arbejder.

![Fanen Indstillinger i gruppen arbejder.][settings-scale]

Derfra er grænsefladen skal være ret velkendte eftersom den den samme oplevelse, at du ser, når du skalere et App-serviceaftale. 

![Indstillinger for manuel skalering.][scale-manual]

Du kan også konfigurere en Autoskalering profil.

![Indstillinger for Autoskalering.][scale-profile]

Autoskalering profiler er nyttige til at angive begrænsninger på din plan. Denne måde, du kan have en ensartet ydeevne oplevelse ved at angive en nedre grænse skaleringsværdi (1) og et mere forudsigelige forbrug knop ved at angive en øvre grænse (2).

![Indstillinger for skalering i profil.][scale-profile2]

Når du definerer en profil, kan du tilføje Autoskalering regler til at skalere op eller ned antallet af forekomster i gruppen arbejder inden for grænserne defineret af profilen. Autoskalering regler er baseret på målepunkter.

![Regel for skala.][scale-rule]

 Alle arbejder puljen eller front end målepunkter kan bruges til at definere Autoskalering regler. Denne statistik er den samme målepunkter, du kan overvåge i ressource blade grafer eller angive beskeder for.

## <a name="autoscale-example"></a>Eksempel på Autoskalering

Autoskalering af et App Service-miljø kan bedst illustreres ved gennemgang af et scenarie.

Denne artikel forklares de nødvendige overvejelser, når du konfigurerer Autoskalering. I artiklen vejleder dig gennem de interaktioner, der kommer ind i play, når du benytter Autoskalering App Service-miljøer, der er hostet i App-tjenesten miljø.

### <a name="scenario-introduction"></a>Scenarie-Introduktion

Frank er en systemadministrator til en enterprise som en del af de arbejdsbelastninger, som han administrerer har overført til en App Service-miljø.

App Service-miljø er konfigureret til Manuel skala på følgende måde:

* **Forrest ender:** 3
* **Arbejder puljen 1**: 10
* **Arbejder puljen 2**: 5
* **Arbejder puljen 3**: 5

Arbejder puljen 1 bruges til fremstilling arbejdsbelastninger, mens arbejder puljen, 2 og arbejder puljen 3 bruges til kvalitetskontrol (QA) og udvikling arbejdsmængder.

App Service-planer til QA og Udviklingscenter er konfigureret med manuel skalering. Fremstilling App-serviceaftale er angivet til Autoskalering håndtere variationer i Indlæs og trafikflow.

Frank er meget fortrolig med programmet. Han ved, at Spidsbelastningstimer for indlæsning er mellem 9:00 AM og 6:00, da dette er et line of business (LOB) program, som medarbejderne bruger, mens de er på kontoret. Brugen udelader efter, når brugerne er færdig, for den pågældende dag. Uden for Spidsbelastningstimer er der stadig nogle Indlæs da brugere kan få adgang til appen fra en fjernplacering ved hjælp af deres mobilenheder eller private computere. Fremstilling App-serviceaftale er allerede konfigureret til at Autoskalering baseret på CPU-brug med følgende regler:

![Bestemte indstillinger for LOB app.][asp-scale]

|   **Autoskalering profil – ugedage – App-serviceaftale**     |   **Autoskalering profil – weekender – App-serviceaftale**     |
|   ----------------------------------------------------    |   ----------------------------------------------------    |
|   **Navn:** Ugedag profil                               |   **Navn:** Weekend profil                               |
|   **Skalere ved:** Regler for tidsplan og ydeevne            |   **Skalere ved:** Regler for tidsplan og ydeevne            |
|   **Profil:** Ugedage                                   |   **Profil:** Weekend                                    |
|   **Type:** Gentagelse                                    |   **Type:** Gentagelse                                    |
|   **TARGET område:** 5-20 forekomster                     |   **TARGET område:** 3-10 forekomster                     |
|   **Dage:** Mandag, tirsdag, onsdag, torsdag, fredag  |   **Dage:** Lørdag, søndag                              |
|   **Starttidspunkt:** 9:00 AM                                 |   **Starttidspunkt:** 9:00 AM                                 |
|   **Tidszone:** UTC-08                                   |   **Tidszone:** UTC-08                                   |
|                                                           |                                                           |
|   **Autoskalering regel (skala op)**                           |   **Autoskalering regel (skala op)**                           |
|   **Ressource:** Fremstilling (App Service miljø)      |   **Ressource:** Fremstilling (App Service miljø)      |
|   **Metrisk:** CPU %                                       |   **Metrisk:** CPU %                                       |
|   **Handling:** Større end 60%                         |   **Handling:** Større end 80%                         |
|   **Varighed:** 5 minutter                                 |   **Varighed:** 10 minutter                                |
|   **Tidspunktet for sammenlægning:** Gennemsnit                           |   **Tidspunktet for sammenlægning:** Gennemsnit                           |
|   **Handling:** Øge antallet af 2                         |   **Handling:** Øge antallet af 1                         |
|   **Smarte ned (minutter):** 15                             |   **Smarte ned (minutter):** 20                             |
|                                                           |                                                           |
  	|   **Autoskalering regel (skala ned)**                     |   **Autoskalering regel (skala ned)**                         |
|   **Ressource:** Fremstilling (App Service miljø)      |   **Ressource:** Fremstilling (App Service miljø)      |
|   **Metrisk:** CPU %                                       |   **Metrisk:** CPU %                                       |
|   **Handling:** Mindre end 30%                            |   **Handling:** Mindre end 20%                            |
|   **Varighed:** 10 minutter                                |   **Varighed:** 15 minutter                                |
|   **Tidspunktet for sammenlægning:** Gennemsnit                           |   **Tidspunktet for sammenlægning:** Gennemsnit                           |
|   **Handling:** Reducere antallet af 1                         |   **Handling:** Reducere antallet af 1                         |
|   **Smarte ned (minutter):** 20                             |   **Smarte ned (minutter):** 10                             |

### <a name="app-service-plan-inflation-rate"></a>App Service plan inflationsraten

App Service planer, der er konfigureret til Autoskalering gøre det med en maksimal hastighed i timen. Denne sats kan beregnes ud fra de værdier, der er angivet på reglen, autoskalering.

Forstå og beregne *App Service plan inflationsraten* er vigtige for App Service miljø Autoskalering, fordi skalering ændres til en kollega puljen ikke omgående.

App Service plan inflationsraten beregnes på følgende måde:

![App Service plan inflationsraten beregning.][ASP-Inflation]

Baseret på Autoskalering – skala op regel for ugedag profilen for fremstilling App-serviceaftale:

![App Service plan inflationsraten for ugedage baseret på Autoskalering – skala op regel.][Equation1]

Formlen vil tale om Autoskalering – skala op regel for Weekend profilen for fremstilling App-serviceaftale Afslut til:

![App Service plan inflationsraten for weekender baseret på Autoskalering – skala op regel.][Equation2]

Denne værdi kan også beregnes for skala ned handlinger.

Baseret på Autoskalering – skala ned regel for ugedag profilen for fremstilling App-serviceaftale dette vil se ud som følger:

![App Service plan inflationsraten for ugedage baseret på Autoskalering – skala ned regel.][Equation3]

Formlen vil tale om Autoskalering – skala ned regel for Weekend profilen for fremstilling App-serviceaftale Afslut til:  

![App Service plan inflationsraten for weekender baseret på Autoskalering – skala ned regel.][Equation4]

Fremstilling App-serviceaftale kan vokse på en maksimal hastighed på otte forekomster/time inden for uge og fire forekomster/time under weekenden. Det kan frigive forekomster med en maksimal hastighed af fire forekomster/time inden for uge og seks forekomster/time under weekender.

Hvis der er tilknyttet flere App tjenesteplanerne i en kollega gruppe, du skal beregne det *samlede inflationsraten* som summen af inflationsraten for alle App Service-planer, der er vært for i gruppe, der arbejder.

![Samlet inflationsraten beregning for flere App Service-planer, der er hostet i en kollega pulje.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Bruge App Service plan inflationsraten til at definere arbejder puljen Autoskalering regler

Arbejder grupper, der er vært App Service planer, der er konfigureret til Autoskalering skal tildeles en bufferen af kapacitet. Bufferen giver mulighed for Autoskalering handlinger til at forstørre og formindske App serviceaftale efter behov. Mindste bufferen ville være den beregnede samlede App Service planlægge inflationsraten.

Fordi App Service miljø skala handlinger gå et stykke tid at anvende, skal eventuelle ændringer højde for yderligere demand ændringer, der kan ske, mens en handling af skala er i gang. For at imødekomme denne ventetid, anbefaler vi, at du bruger den beregnede samlede App Service planlægge inflationsraten som det mindste antal forekomster, der er føjet til hver Autoskalering handling.

Disse oplysninger kan Frank definere følgende Autoskalering profil og regler:

![Autoskalering profil regler for LOB eksempel.][Worker-Pool-Scale]

|   **Autoskalering profil – ugedage**                        |   **Autoskalering profil – weekender**                |
|   ----------------------------------------------------    |   --------------------------------------------    |
|   **Navn:** Ugedag profil                               |   **Navn:** Weekend profil                       |
|   **Skalere ved:** Regler for tidsplan og ydeevne            |   **Skalere ved:** Regler for tidsplan og ydeevne    |
|   **Profil:** Ugedage                                   |   **Profil:** Weekend                            |
|   **Type:** Gentagelse                                    |   **Type:** Gentagelse                            |
|   **TARGET område:** 13 til 25 forekomster                    |   **TARGET område:** 6 til 15 forekomster             |
|   **Dage:** Mandag, tirsdag, onsdag, torsdag, fredag  |   **Dage:** Lørdag, søndag                      |
|   **Starttidspunkt:** 7:00 AM                                 |   **Starttidspunkt:** 9:00 AM                         |
|   **Tidszone:** UTC-08                                   |   **Tidszone:** UTC-08                           |
|                                                           |                                                   |
|   **Autoskalering regel (skala op)**                           |   **Autoskalering regel (skala op)**                   |
|   **Ressource:** Arbejder puljen 1                             |   **Ressource:** Arbejder puljen 1                     |
|   **Metrisk:** WorkersAvailable                            |   **Metrisk:** WorkersAvailable                    |
|   **Handling:** Mindre end 8                              |   **Handling:** Mindre end 3                      |
|   **Varighed:** 20 minutter                                |   **Varighed:** 30 minutter                        |
|   **Tidspunktet for sammenlægning:** Gennemsnit                           |   **Tidspunktet for sammenlægning:** Gennemsnit                   |
|   **Handling:** Øge antallet af 8                         |   **Handling:** Øge antallet af 3                 |
|   **Smarte ned (minutter):** 180                            |   **Smarte ned (minutter):** 180                    |
|                                                           |                                                   |
|   **Autoskalering regel (skala ned)**                         |   **Autoskalering regel (skala ned)**                 |
|   **Ressource:** Arbejder puljen 1                             |   **Ressource:** Arbejder puljen 1                     |
|   **Metrisk:** WorkersAvailable                            |   **Metrisk:** WorkersAvailable                    |
|   **Handling:** Større end 8                           |   **Handling:** Større end 3                   |
|   **Varighed:** 20 minutter                                |   **Varighed:** 15 minutter                        |
|   **Tidspunktet for sammenlægning:** Gennemsnit                           |   **Tidspunktet for sammenlægning:** Gennemsnit                   |
|   **Handling:** Mindske Tæl med 2                         |   **Handling:** Mindske Tæl med 3                 |
|   **Smarte ned (minutter):** 120                            |   **Smarte ned (minutter):** 120                    |

Det Target område, der er defineret i profilen beregnes ved de mindste forekomster, der er defineret i profilen, så på App-serviceaftale + bufferen.

Området maksimum ville være summen af alle de maksimale områder for alle App Service-planer, der arbejder puljen som vært.

Forøg antallet for skalaen regler skal du vælge mindst 1 X inflationsraten App Service Plan for skala.

Formindsk Tæl kan justeres til noget mellem 1/2 X eller 1 X inflationsraten App Service Plan for skala ned.

### <a name="autoscale-for-front-end-pool"></a>Autoskalering til front end-puljen

Regler for front end Autoskalering er nemmere end til arbejder grupper. Primært, bør du  
Sørg for, at varigheden af målingen og cooldown timerne overvejer, skala handlinger på en App-serviceaftale ikke er omgående.

I dette scenarie ved Frank, fejl rente øges, når forside ender når CPU-forbrug 80%, og angiver Autoskalering reglen at øge forekomster på følgende måde:

![Autoskalering indstillinger for front end-puljen.][Front-End-Scale]

|   **Autoskalering profil – foran slutter**              |
|   --------------------------------------------    |
|   **Navn:** Autoskalering – foran slutter                |
|   **Skalere ved:** Regler for tidsplan og ydeevne    |
|   **Profil:** For hver dag                           |
|   **Type:** Gentagelse                            |
|   **TARGET område:** 3-10 forekomster             |
|   **Dage:** For hver dag                              |
|   **Starttidspunkt:** 9:00 AM                         |
|   **Tidszone:** UTC-08                           |
|                                                   |
|   **Autoskalering regel (skala op)**                   |
|   **Ressource:** Front end-puljen                    |
|   **Metrisk:** CPU %                               |
|   **Handling:** Større end 60%                 |
|   **Varighed:** 20 minutter                        |
|   **Tidspunktet for sammenlægning:** Gennemsnit                   |
|   **Handling:** Øge antallet af 3                 |
|   **Smarte ned (minutter):** 120                    |
|                                                   |
|   **Autoskalering regel (skala ned)**                 |
|   **Ressource:** Arbejder puljen 1                     |
|   **Metrisk:** CPU %                               |
|   **Handling:** Mindre end 30%                    |
|   **Varighed:** 20 minutter                        |
|   **Tidspunktet for sammenlægning:** Gennemsnit                   |
|   **Handling:** Mindske Tæl med 3                 |
|   **Smarte ned (minutter):** 120                    |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
