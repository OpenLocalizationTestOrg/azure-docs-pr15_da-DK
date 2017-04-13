<properties 
   pageTitle="Azure Mobile aftale fejlfindingsvejledningen - Push/rækkevidde" 
   description="Fejlfinding af problemer med bruger interaktion og meddelelse i Azure Mobile aftale" 
   services="mobile-engagement" 
   documentationCenter="" 
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

# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Fejlfinding i forbindelse med vejledning til Push og få adgang til problemer

Følgende er mulige problemer, der kan opstå med hvordan Azure Mobile aftale sender oplysninger til dine brugere.
 
## <a name="push-failures"></a>Push-fejl

### <a name="issue"></a>Problem
- Skubber virker ikke (i app af app eller begge).

### <a name="causes"></a>Årsager
- Mange gange en opslagsnål fejl er angivelse, Azure Mobile aftale, rækkevidde eller en anden avanceret funktion i Azure Mobile aftale ikke korrekt er integreret eller opgraderes er påkrævet i SDK at løse et kendt problem med en ny OS eller enhed platform.
- Teste bare en App i opslagsnål og bare en ud i App opslagsnål til at afgøre, om noget er et problem, der i App eller ud i App.
- Test af Brugergrænsefladen såvel API som et trin i fejlfindingen til at se, hvilke yderligere fejloplysninger findes begge steder.
- Skubber fungerer ikke, medmindre både Azure Mobile aftale og rækkevidde er integreret i SDK af app'en.
- Skubber fungerer ikke, hvis certifikater ikke er gyldig eller bruger Prod.ordre kontra Udviklingscenter korrekt (kun iOS). (**Note:** "af app" pushmeddelelser må ikke leveres til iOS, hvis du har både udviklingen (Udviklerværktøjer) og fremstilling (PRODUKTIONSORDRERUTESTYRING) versioner af programmet installeret på den samme enhed, da sikkerhedstokenet, der er knyttet til dit certifikat erklæres af Apple. Du kan løse dette problem ved Fjern begge Udviklingscenter og PROD versioner af dit program og Installer igen kun én version på din enhed.)
- Af App opslagsnål håndteres tæller forskelligt i forskellige platforme (iOS viser mindre oplysninger end Android, hvis oprindelige skubber er deaktiveret på en enhed, API kan give mere end Brugergrænsefladen på opslagsnål statistik).
- Skubber kan blive blokeret af kunder på OS niveau (iOS og Android) af app'en.
- Af app'en vises skubber som deaktiveret i Azure Mobile aftale Brugergrænsefladen, hvis de ikke er integreret korrekt, men ikke muligvis automatisk fra API.
- I App virker skubber ikke, medmindre både Azure Mobile aftale og rækkevidde er integreret i SDK.
- GCM og ADM skubber virker ikke, medmindre Azure Mobile aftale, og den specifikke server er integreret i SDK (kun Android).
- I App og ikke til App skubber skal bruges til test separat for at afgøre, om det er et problem med opslagsnål eller rækkevidde.
- I App skubber kræver, at appen være åben der modtages.
- I App er skubber ofte konfiguration skal filtreres efter et tilføjelsesprogram eller tilmelding fra app oplysninger mærke.
- Hvis du bruger en brugerdefineret kategori i rækkevidde skal vises i app-meddelelser, du skal følge den korrekte livscyklus i meddelelsen om, ellers meddelelsen kan ikke slettes når brugeren afvise den.
- Hvis du starter en kampagnens med ingen slutdato og en enhed modtager i app-besked men bruges ikke vist det endnu, bruger vil stadig modtager denne besked næste gang de logge på appen, selvom du manuelt afslutte kampagnens.
- Kontrollér, at du virkelig vil bruger API'EN Push i stedet for når API (da API når bruges oftere) og, at du ikke forvirrende parametrene "data" og "anmelder" problemer med Push-API.
- Teste din opslagsnål kampagnens med både en enhed, der er forbundet via Wi-Fi og 3G for at eliminere netværksforbindelsen som en mulig årsag til problemer.

## <a name="push-testing"></a>Push-test

### <a name="issue"></a>Problem
- Skubber kan sendes til en bestemt enhed, der er baseret på en enheds-ID.

### <a name="causes"></a>Årsager

- Test enheder er konfiguration anderledes for hver platform, men medfører en begivenhed i din app på en enhed med test og leder du efter din enheds-ID i portalen skal bruge for at finde din enheds-ID for alle platforme.
- Test enheder fungerer anderledes med IDFA kontra IDFV (kun iOS).


## <a name="push-customization"></a>Push-tilpasning

### <a name="issue"></a>Problem
- Avancerede opslagsnål element ikke fungerer (badge ringer vibrationer, billede, og osv.).
- Links fra skubber fungerer ikke (ikke-app, i app til et websted til en placering i app).
- Push statistik viser, at en opslagsnål ikke blev sendt til så mange mennesker som forventet (for mange eller er ikke nok).
- Push dubleret og modtaget to gange.
- Kan ikke registrere test enhed til Azure Mobile aftale skubber (med dine egne Prod.ordre eller Udviklerværktøjer app).

### <a name="causes"></a>Årsager

- Hvis du vil oprette et link til en bestemt placering i app kræver "kategorier" (kun Android).
- Deep sammenkæde farveskemaer til at omdirigere brugere til en anden placering, når du klikker på en meddelelse om opslagsnål skal oprettes i og administreres af dit program og enheden OS ikke efter Mobile aftale direkte. (**Note:** af app'en meddelelser kan ikke sammenkæde direkte til på app placeringer med iOS som de kan med Android.)
- Billede af eksterne servere har brug for at kunne bruge HTTP "Hent" og "Hoved" for overblik flytter til at fungere sammen (kun Android).
- I din kode, kan du deaktivere Azure Mobile aftale agent, når tastaturet åbnes og koden skal aktivere Azure Mobile aftale agent igen, når tastaturet er lukket, så tastaturet ikke påvirker udseendet af din meddelelse (kun iOS).
- Nogle elementer virker ikke i test simulering, men kun reelle kampagner (badge ringer vibrationer, billede, og osv.).
- Ingen server side data logføres, når du bruger knappen "teste" skubber. Data er kun logget for reelle opslagsnål kampagner.
- Fejlfinding af med til at isolere problemet,: test, simulere, og et reelt tal kampagnens, da de hver fungerer lidt anderledes.
- Hvor lang tid din "i app" og "som helst" kampagner er planlagt til at køre kan effekt levering tal, da en kampagnens vil kun blive leveret til brugere, der er "i app", mens kampagnens kører (og brugere, som har deres Enhedsindstillinger, der er indstillet til at modtage beskeder "af app").
- Forskelle mellem hvordan Android og iOS håndtere af app-meddelelser gør det svært at sammenligne direkte opslagsnål statistik for Android- og iOS-versionen af dit program. Android indeholder flere OS niveau meddelelse oplysninger end iOS gør. Android rapporter, når en oprindelig meddelelse er modtaget, har klikket på eller slettet i meddelelse center, men iOS rapporterer ikke disse oplysninger, medmindre der klikkes på meddelelsen. 
- Den primære årsagen til, at "trykket" tal ser anderledes ud end andet end "leveret" tal for når kampagner er, at "i app" og "ikke app" beskeder, tælles anderledes. "I app" meddelelser håndteres af Mobile aftale, men meddelelser håndteres "af app" af besked om center i OS på din enhed.

## <a name="push-targeting"></a>Push-målretning af

### <a name="issue"></a>Problem
- Indbygget i målretning af virker ikke som forventet.
- App oplysninger mærke målretning virker ikke som forventet.
- Geografisk placering målretning af virker ikke som forventet.
- Sprogindstillinger fungerer ikke som forventet.

### <a name="causes"></a>Årsager

- Sørg for, at du har overført app oplysninger mærker via Azure Mobile aftale brugergrænseflade eller API.
- (Throttling) opslagsnål hastighed eller push kvote på niveauet for programmet eller begrænse målgruppen på niveauet for kampagnens kan forhindre en person i at modtage en bestemt opslagsnål, selvom de opfylder dine andre målretning kriterier. 
- Angive et "sprog" er anderledes end målretning af baseret på land eller landestandard, der er også forskellig fra målretning af baseret på geografisk placering, der er baseret på en telefon placering eller GPS-placering.
- Meddelelsen "standardsproget" sendes til kunder, der ikke har deres enhed, der er indstillet til en af de alternative sprog, du angiver.


## <a name="push-scheduling"></a>Push-planlægning

### <a name="issue"></a>Problem
- Push planlægning fungerer ikke som forventet (sendt for tidlig eller senere).

### <a name="causes"></a>Årsager

- Tidszoner kan problemer i forbindelse med planlægning, især hvis ved hjælp af den slutbrugere tidszone.
- Avancerede opslagsnål funktioner kan forsinke skubber.
- Målretning af baseret på telefon indstillinger (i stedet for App oplysninger mærker), kan det forsinke flytter da Azure Mobile aftale kan være nødvendigt at anmode om data fra realtid telefon før afsendelse af en opslagsnål.
- Kampagner, der er oprettet uden en slutdato for gemme tryk lokalt på enheden, og Vis den næste gang appen åbnes, selvom kampagnens afbrydes manuelt.
- Starte mere end én kampagnens ad gangen, kan det tage længere tid at scanne dine bruger base (forsøg at kun starte én kampagnens ad gangen med højst fire, også target kun til dit aktive brugere så gamle brugere ikke behøver at være scannet).
- Hvis du bruger indstillingen "Ignorer målgruppe opslagsnål sendes til brugere via API" i afsnittet "Kampagnens" i en rækkevidde kampagnens kampagnens sender ikke automatisk, skal du sende den via API når manuelt.
- Hvis du bruger en brugerdefineret kategori i rækkevidde skal vises i app-meddelelser, du skal følge den korrekte livscyklussen for en meddelelse, ellers meddelelsen kan ikke slettes når brugeren afvise den.

 
