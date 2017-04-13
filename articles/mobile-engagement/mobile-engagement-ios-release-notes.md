<properties
    pageTitle="Azure Mobile aftale iOS SDK produktbemærkninger | Microsoft Azure"
    description="Seneste opdateringer og procedurer til iOS SDK til Azure Mobile aftale"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="piyushjo" />

#<a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Azure Mobile aftale iOS SDK produktbemærkninger

##<a name="400-09122016"></a>4.0.0 (09-12/2016)

-   Fast meddelelse ikke actioned på iOS 10-enheder.
-   Fraråde XCode 7.

##<a name="324-06302016"></a>3.2.4 (06-30/2016)

-   Fast sammenlægning mellem tekniske logfiler og andre logfiler.

##<a name="323-06072016"></a>3.2.3 (06-07/2016)

-   Fast fejlen, hvor levering feedback er ikke rapporteret, da app'en kører i baggrunden.
-   Optimeret afsendelse af tekniske logfiler.

##<a name="322-04072016"></a>3.2.2 (04-07/2016)

-   Der er rettet på http-anmodning om annulleringen hvilket nogle gange fører til at gå ned.

##<a name="321-12112015"></a>3.2.1 (12-11-2015)

-   Fast forsinkelsen, når en ny app-forekomst udløses af en meddelelse med deep links

##<a name="320-10082015"></a>3.2.0 (10-08-2015)

-   Aktiveret Bitcode i SDK at gøre det til at fungere med **Xcode 7**.
-   Fast fejl, der er relateret til i app'en beskeder.
-   Gjort i app-meddelelser mere pålidelig i tilfælde af lavt batteriniveau og andre disse scenarier.
-   Fjernet ekstra console logfiler, der genereres af 3 part bibliotek.

##<a name="310-08262015"></a>3.1.0 (08-26-2015)

-   Ret iOS 9 kompatibilitet fejl med et bibliotek fra tredjepart. Det var årsag går ned, mens sende undersøger resultater, programoplysninger eller ekstra data.

##<a name="300-06192015"></a>3.0.0 (06-19-2015)

-   Mobile aftale bruger uovervåget Push-beskeder.
-   Sænket support til iOS 4.X. Start fra denne version installationsdestinationen for dit program skal være mindst iOS 6.

##<a name="220-05212015"></a>2.2.0 (05-21-2015)

-   Mobile aftale enheds-id til enheder < iOS 6 er nu baseret på en GUID, der er oprettet under installationen.

##<a name="210-04242015"></a>2.1.0 (04-24-2015)

-   Tilføjede hurtig kompatibilitet.
-   Når du klikker på en meddelelse, udført handlingen URL-adressen er nu højre, når programmet åbnes.
-   Tilføjede manglende sidehoved fil i SDK-pakken.
-   Fast et problem, når Mobile aftale nedbrud reporter blev deaktiveret.

##<a name="200-02172015"></a>2.0.0 (02-17-2015)

-   Første version af Azure Mobile aftale
-   konfiguration af sikkerhedskontrol/sdkKey erstattes af en streng forbindelseskonfiguration.
-   Fjernet API til at sende og modtage vilkårlig XMPP meddelelser fra vilkårlig XMPP enheder.
-   Fjernet API til at sende og modtage meddelelser mellem enheder.
-   Forbedringer af sikkerhed.
-   SmartAd registrering fjernet.
