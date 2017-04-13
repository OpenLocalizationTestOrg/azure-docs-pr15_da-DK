<properties
    pageTitle="Azure Mobile aftale Android SDK Integration"
    description="Seneste opdateringer og procedurer for Android SDK til Azure Mobile aftale"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo" />

# <a name="release-notes"></a>Produktbemærkninger

## <a name="423-08102016"></a>4.2.3 (08-10/2016)

- Ingen yderligere WIFI Lås.
- Ret baglås, når du ringer til getDeviceId før initialisering (fejldatabase introduceret i 4.2.0).

## <a name="422-05172016"></a>4.2.2 (05-17/2016)

- Forbedringer af stabilitet.

## <a name="421-05102016"></a>4.2.1 (05-10/2016)

- Sikkerhed: Deaktiver Webadgang fra visningen lokal fil.
- Sikkerhed: fjerne `EngagementPreferenceActivity` klasse, der udvider forældede og usikker `PreferenceActivity` klasse.
- Sikkerhed: rækkevidde aktiviteter er nu dokumenteret for at bruge `exported="false"`, dette flag kan også bruges i tidligere versioner af SDK.

## <a name="420-03112016"></a>4.2.0 (03-11/2016)

- SDK er nu licenseret under MIT.
- Tillad, at angive et brugerdefineret enheds-id på SDK initialisering tid.

## <a name="415-02012016"></a>4.1.5 (02-01/2016)

- Forbedringer af stabilitet.

## <a name="414-01262016"></a>4.1.4 (01/26/2016)

- Forbedringer af stabilitet.

## <a name="413-1292015"></a>4.1.3 (12-9-2015)

- Forbedringer af stabilitet.

## <a name="412-11252015"></a>4.1.2 (11-25-2015)

- Forbedringer af stabilitet.

## <a name="411-11042015"></a>4.1.1 (11-04-2015)

- Forbedringer af stabilitet.

## <a name="410-08252015"></a>4.1.0 (08-25-2015)

- Håndtere nye tilladelsesmodel for Android M.
- Nu kan konfigurere funktioner til placering på kørselstidspunktet i stedet for ved hjælp af `AndroidManifest.xml`.
- Løse en tilladelse-fejl: Hvis du bruger `ACCESS_FINE_LOCATION`, derefter `ACCESS_COARSE_LOCATION` er ikke længere er nødvendig.
- Forbedringer af stabilitet.

## <a name="400-07062015"></a>4.0.0 (07-06-2015)

-   Interne protocol ændringer til at gøre analyser og opslagsnål mere pålidelig.
-   Oprindelig opslagsnål (GCM/ADM) nu bruges også til i app-meddelelser så skal du konfigurere de oprindelige opslagsnål legitimationsoplysninger for alle typer opslagsnål kampagnens.
-   Løse overblik meddelelse: det var viste 10 'er kun efter der overføres.
-   Rette en fejl i webvisning: at klikke på et link udførte også standard handling URL-adressen.
-   Ret sjældne nedbrud relateret til lokale Lagerstyring.
-   Ret dynamisk streng Konfigurationsstyring.
-   Opdater slutbrugerlicensaftale (EULA).

## <a name="300-02172015"></a>3.0.0 (02-17-2015)

-   Første version af Azure Mobile aftale
-   konfiguration af sikkerhedskontrol erstattes af en streng forbindelseskonfiguration.
-   Fjernet API til at sende og modtage vilkårlig XMPP meddelelser fra vilkårlig XMPP enheder.
-   Fjernet API til at sende og modtage meddelelser mellem enheder.
-   Forbedringer af sikkerhed.
-   Google Play og SmartAd fjernes.
