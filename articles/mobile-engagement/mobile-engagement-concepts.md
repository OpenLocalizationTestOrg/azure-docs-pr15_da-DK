<properties
    pageTitle="Mobile aftale begreber | Microsoft Azure"
    description="Azure Mobile aftale begreber"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Azure Mobile aftale begreber

Mobile aftale definerer et par almindelige begreber til alle understøttede platforme. I denne artikel beskrives kortvarigt disse begreber.

I denne artikel er en god start, hvis du er ny bruger af Mobile aftale. Også sørge for at læse den dokumentation, der er specifikke for den platform, du bruger, som det vil tilpasse de begreber, der er beskrevet i denne artikel med flere detaljer og eksempler samt mulige begrænsninger.

## <a name="devices-and-users"></a>Enheder og brugere
Mobile aftale identificerer brugere ved at generere et entydigt id for hver enhed. Dette id kaldes enheds-id (eller `deviceid`). Den er oprettet i en sådan måde, at alle programmer kører på den samme enhed dele den samme enheds-id.

Implicit, betyder det, Mobile aftale finder en enhed skal tilhøre nøjagtigt én bruger, og dermed, brugere og enheder er tilsvarende begreber.

## <a name="sessions-and-activities"></a>Sessioner og aktiviteter
En session er en anvendelse af det program, der er udført af en bruger, fra tidspunktet, brugeren begynder at bruge det, indtil brugeren Stop.

En aktivitet er en anvendelse af en given underordnede del af det program, der er udført af én bruger (det er som regel skærmen, men det kan være noget egnet til programmet).

En bruger kan kun udføre en aktivitet ad gangen.

En aktivitet er identificeret med et navn (begrænset til 64 tegn) og eventuelt kan integrere nogle ekstra data (i grænsen på 1024 byte).

Sessioner beregnes automatisk fra rækkefølgen af aktiviteter, der er udført af brugere. En session starter, når brugeren starter sin første aktivitet og stopper, når hun er færdig med at sin sidste aktivitet. Det betyder, at en session ikke behøver at være eksplicit startes eller stoppes. Aktiviteter, der startes i stedet eksplicit eller ikke længere. Hvis nogen aktivitet rapporteres, rapporteres ingen session.

## <a name="events"></a>Begivenheder
Hændelser, der bruges til at rapportere Chat handlinger (som knappen trykkes eller artikler, der læses af brugere).

En begivenhed kan være relateret til den aktuelle session, til et job, der kører, eller det kan være en enkeltstående hændelse.

En hændelse er identificeret med et navn (begrænset til 64 tegn) og eventuelt kan integrere nogle ekstra data (i grænsen på 1024 byte).

## <a name="error"></a>Fejl
Fejl, der bruges til at rapportere problemer, der er registreret korrekt af programmet (som forkert brugerhandlinger eller API opkald fejl).

Fejlen kan være relateret til den aktuelle session, til et job, der kører, eller det kan være en enkeltstående fejl.

Fejlen er identificeret med et navn (begrænset til 64 tegn) og eventuelt kan integrere nogle ekstra data (i grænsen på 1024 byte).

## <a name="job"></a>Job
Job bruges til at rapportere handlinger, der har en varighed (som varighed af API-kald, få vist klokkeslættet for reklamer, varigheden af opgaver i baggrunden eller varigheden af brugerhandlinger).

Et job er ikke relateret til en session, fordi en opgave kan udføres i baggrunden, uden brugerinput.

Et job er identificeret med et navn (begrænset til 64 tegn) og eventuelt kan integrere nogle ekstra data (i grænsen på 1024 byte).

## <a name="crash"></a>Gå ned
Nedbrud udstedes automatisk af Mobile aftale SDK, til at rapportere programfejl, hvor problemer, der er ikke registreret af programmet gør det ned gå ned.

## <a name="application-information"></a>Følgende oplysninger om
Programoplysninger (eller app oplysninger) bruges til at mærke brugere, det vil sige, for at knytte nogle data til brugerne af et program (dette er ligner web cookies, bortset fra at app oplysninger er gemt på serversiden på Azure Mobile aftale platformen).

App oplysninger kan registreres, ved hjælp af Mobile aftale SDK API eller ved hjælp af Mobile aftale platform enhed API.

App oplysninger er et nøgle/værdi-par, der er knyttet til en enhed. Tasten er navnet på den app oplysninger (begrænset til 64 ASCII-bogstaver [a-hamza-Z], [0-9] tal og understregninger [_]). Værdien (begrænset til 1024 tegn) kan være en streng, heltal, dato (åååå-MM-dd) eller Boolesk (SAND eller FALSK).

Et vilkårligt antal app oplysninger kan knyttes til en enhed, inden for grænserne defineret af Mobile aftale priser betingelser. For en given nøgle Mobile aftale kun holder styr på det seneste værdisæt (ingen oversigt). Angive eller ændre værdien af en app oplysninger tvinger Mobile aftale til at evaluere målgruppe kriterier Indstil på denne app oplysninger (hvis relevant), hvilket betyder, at disse app oplysninger kan bruges til at udløse realtid skubber.

## <a name="extra-data"></a>Ekstra data
Ekstra data (eller ekstra) er nogle vilkårlig data, som kan knyttes til begivenheder, fejl, aktiviteter og job.

Ekstra er struktureret på samme måde til JSON objekter: de foretages af et træ nøgle/værdi-par. Taster er begrænset til 64 ASCII-bogstaver [a-hamza-Z], tallene 0-9 og understregningstegn [_]) og den samlede størrelse af ekstra er begrænset til 1024 tegn (én gang kodet i JSON ved Mobile aftale SDK).

Hele træet af nøgle/værdi-par gemmes som en JSON-objekt. Dog kun det første niveau af nøgler/værdier er delt er direkte tilgængelige for visse avancerede funktioner som segmenter (for eksempel du kan let at definere en målgruppe, kaldet "SciFi blæsere", der er foretaget af alle brugere, der har sendt mindst 10 gange hændelsen med navnet "content_viewed" med den ekstra vigtige "content_type" angivet til værdien "scifi" i den sidste måned). Dermed anbefales til at sende kun extras, der er foretaget af enkle lister over nøgle/værdi-par ved hjælp af skalær værdier (for eksempel strenge, datoer, heltal eller boolesk).

## <a name="next-steps"></a>Næste trin

- [Windows Universal SDK oversigt til Azure Mobile aftale](mobile-engagement-windows-store-sdk-overview.md)
- [Windows Phone Silverlight SDK oversigt til Azure Mobile aftale](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK til Azure Mobile aftale](mobile-engagement-ios-sdk-overview.md)
- [Android SDK til Azure Mobile aftale](mobile-engagement-android-sdk-overview.md)
