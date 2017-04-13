<properties 
   pageTitle="Azure Mobile aftale fejlfinding i forbindelse med hjælpelinjer" 
   description="Fejlfinding i forbindelse med vejledning til Azure Mobile aftale" 
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

# <a name="azure-mobile-engagement---troubleshooting-guide"></a>Azure Mobile aftale - fejlfindingsvejledningen

## <a name="introduction"></a>Introduktion
Den følgende fejlfinding vejledning hjælper dig med at forstå grundlæggende årsager over nogle ofte vist problemer og er gør det muligt at udføre fejlfinding på din egen. 

## <a name="general"></a>Generelt

Generelt, skal du altid sikre følgende:

1. Sørg for, at du har gennemført alle de trin, der kræves til integration, som beskrevet i vores [Introduktion selvstudier](mobile-engagement-windows-store-dotnet-get-started.md)
2. Du bruger den nyeste version af platform SDK'er. 
3. Teste på både en faktisk enhed og en emulator, da nogle problemer er specifikke for emulator kun. 
4. Er du ikke at trykke på en hvilken som helst begrænsninger/throttles fra Mobile aftale som er beskrevet [her](../azure-subscription-service-limits.md)
5. Hvis du ikke kunne oprette forbindelse til den Mobile aftale service backend-version eller se data ikke blive indlæst løbende derefter sikre dig, at der er ingen igangværende tjenestehændelser ved at markere [her](https://azure.microsoft.com/status/)

## <a name="monitor-issues"></a>'' Skærmproblemer

### <a name="i-am-not-seeing-my-device-showing-up-on-the-monitor-tab"></a>Jeg kan ikke se min enhed, der vises under fanen skærm
Under fanen skærm viser de enheder, der er knyttet til din Mobile aftale platform i realtid. Hvis du foretager fejlfinding på en emulator og enhed, skal du se afsnittet mindst én session her. Hvis appen er fordelt, får du vist den aktive sessioner måler afspejler de enheder, som er forbundet med platformen i realtid. 

Hvis du ikke kan se din enhed under fanen skærm er det sandsynligvis et SDK integration problem. Nogle almindelige trin til fejlfinding i forbindelse med er som følger:

1. Sikre, at du bruger den korrekte forbindelsesstreng i mobilappen og den kommer fra sektionen SDK nøgler og ikke API taster sektionen. Din mobilapp forbindelsesstrengen opretter forbindelse til forekomsten af appen Mobile aftale, hvor du vil se din enhed under fanen skærm. 
2. Til Windows-platform - hvis siden tilsidesætter den `OnNavigatedTo` metode, skal du sørge for at ringe `base.OnNavigatedTo(e)`.
3. Hvis du integrerer Mobile aftale til en eksisterende-mobilappen og derefter kan du også sikre, at du ikke går glip af et trin ved at kigge på avancerede integration trinnene [her](mobile-engagement-windows-store-integrate-engagement.md)
4. Sikre, at du sender mindst én/aktivitet på skærmen ved at tilsidesætte siden med EngagementActivity afhængigt af den platform, du arbejder, som beskrevet i [Introduktion selvstudier](mobile-engagement-windows-store-dotnet-get-started.md).

### <a name="i-am-seeing-the-monitor-tab-showing-a-session-even-when-i-have-disconnected-or-closed-my-app-emulator"></a>Jeg kan se fanen skærm, der viser en session selv når jeg har afbrudt eller lukket min app / emulator. 
Hvis du er den eneste, der er tilsluttet platformen på dette tidspunkt, og du bruger en emulator til at åbne appen er det sandsynligvis på grund af emulator Strict. Generelt, skal du sikre dig, at du kommer tilbage til startskærmen på emulatoren for app-session for at afbryde forbindelsen korrekt. Desuden på Windows-platform, under fejlfinding med Visual Studio, skal du muligvis sikre, at i Visual Studio, skal du gå til menulinjen **Livscyklus begivenheder** og klikke på **pause** virkelig lukke sessionen. Du kan finde oplysninger i [Windows selvstudium](mobile-engagement-windows-store-dotnet-get-started.md) . 

## <a name="analytics-issues"></a>'Analytics' problemer

### <a name="i-am-not-seeing-any-data-refreshed-data-on-analytics-tab"></a>Jeg får ikke vist nogen data / opdateres data under fanen analyser 
Genberegnes Analytics-data med jævne mellemrum, og det kan tage op til 24 timer for denne opdatering. Disse data ikke realtid, og du får vist den opdateres inden for denne 24-timers tidsperiode.
Kontroller dog, du sender mindst ét skærmbillede eller aktivitet til platform back-end ved enten tilsidesætte mindst én side med `EngagementActivity` eller ringer `SendActivity` explcitly. 

### <a name="i-am-seeing-incorrectly-captured-datetime-for-a-device-on-the-analytics-tab"></a>Jeg får vist forkert hentede dato og klokkeslæt for en enhed under fanen analyser
Hvor lang tid for analyser er baseret på datoen fra brugernes enhedsindstillinger. Så sørg for, at enheden har datoen, der er angivet korrekt. 

## <a name="segment-issues"></a>'Segmentet' problemer

### <a name="i-created-a-segment-and-it-is-showing-up-as-greyed-out-or-not-showing-any-data"></a>Jeg har oprettet en målgruppe, og de vises som nedtonet eller ikke vises nogen data
Oprettelse af målgruppen er ikke i realtid i øjeblikket. Det beregnes på samme tid, som sammenlægges analytics-data, og så det kan tage op til 24 timer. Du skal kontrollere igen senere, men du skal du også sørge for, at din mobilapps faktisk sender de data, som du der udgør segmenterne. F.eks. Hvis en begivenhed sig 'foo' ikke sendes af en hvilken som helst mobilenhed og der ville være segmentdata for en målgruppe, der er oprettet med EventName = foo som kriteriet. Du skal også kontrollere din SDK integration at sikre, at din mobile-app bruges til at sende dataene korrekt. 

## <a name="reach-or-push-notifications-issues"></a>'Når' eller Push-beskeder

### <a name="my-push-messages-are-not-being-delivered"></a>Min opslagsnål meddelelser leveres ikke 

1. Prøv at sende meddelelser til en test enhed først at sikre, at alle komponenter - mobilapp, SDK og tjenesten er tilsluttet korrekt og stand til at levere pushmeddelelser. 
2. Send altid nemmeste 'ud i app meddelelsen' først via en kampagnens, ikke er planlagt og eller den har en hvilken som helst målgruppe kriterium, der er angivet. Dette er igen at bevise, at meddelelse connectivity fungerer korrekt. 
3. Hvis du har problemer med at levere i app-meddelelser også er det en god ide at prøve at sende en meddelelse om ud i app først. 
4. Sørg for, på 'oprindelige Push' er korrekt konfigureret for din-mobilappen. Afhængigt af platformen omfatter det enten taster (Android, Windows) eller certifikater (iOS). Se [brugergrænseflade - indstillinger](mobile-engagement-user-interface-settings.md)
5. Af app'en beskeder også blive blokeret af brugeren via mobile OS så sikre, at det ikke er tilfældet. 
6. Sørg for, at du ikke angiver indstillingen *ignorere målgruppe opslagsnål sendes til brugere via API* i afsnittet **kampagnens** i en rækkevidde kampagnens fordi dette sikrer, at pushmeddelelser kun skal sendes via API'er. 
7. Sørg for, at du tester din opslagsnål kampagnens med både en enhed, der er forbundet via Wi-Fi og telefon operator netværk til at fjerne netværksforbindelsen som en mulig årsag til problemer.
8. Sørg for, at system dato og klokkeslæt på enheden/emulator er korrekt, fordi enhver synkroniseret enhed vil også forstyrre Push besked om tjenestens evne til at levere meddelelser. 

Flere platform bestemte fejlfinding vejledningen nedenfor:

1. **iOS** 

    - Sørg for, at certifikaterne er gyldige og resterende til iOS Push-beskeder. 
    - Sørg for, at du vil konfigurere et *fremstilling* certifikat korrekt i din Mobile aftale app. 
    - Sørg for, at du tester på en *reelt tal, fysisk enhed.* IOS simulator kan ikke behandle opslagsnål meddelelser.
    - Sørg for, at det samlet id er korrekt konfigureret i-mobilappen. Se vejledningen [her](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)
    - Når du tester, kan du bruge "Ad Hoc" fordeling i din mobile klargøring profil. Du vil ikke kunne modtage besked, hvis din app er kompileret ved hjælp af "Fejlfinding"

2. **Android**

    - Sørg for, at du har angivet den korrekte projektnummer i din mobilapp AndroidManifest.xml-fil som efterfølges af \n tegn. 
    
            <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
        
    - Sikre, at du ikke er manglende eller forkert konfigureret alle tilladelser i Android manifestfilen 
    - Sørg for, at det projektnummer, du vil føje til din klient app kommer fra den samme konto, hvor du har fået tasten GCM Server. En hvilken som helst uoverensstemmelsen mellem to forhindrer, at din skubber gå. 
    - Hvis du får vist systemmeddelelser, men ikke i app og derefter gennemse [Angiv et ikon for beskeder sektion](mobile-engagement-android-get-started.md) som dermed du ikke angiver det korrekte ikon i filen Android manifestet. 
    - Hvis du sender en meddelelse om BigPicture, skal du sikre dig, at hvis du har eksterne billede servere og derefter de skal kunne support HTTP "Hent" og "Afsnit".

3. **Windows**
    
    - Sørg for, at du har knyttet appen med et gyldigt Windows Store app. I Visual Studio - skal du du højreklikke på projektet og vælg "Knytte App med Store" indstilling og vælge den app, du har oprettet i Windows Store. Denne Windows Store app skal være det samme, fra hvor du har fået de oprindelige opslagsnål legitimationsoplysninger til at konfigurere på portalen Mobile aftale.
    - Hvis du modtager ud i app pushmeddelelser, men ikke i app-meddelelser med `EngagementOverlay` integration derefter sikre, at der er et gitter rodelementet på siden. EngagementOverlay bruger det første "Gitter" element, der er angivet i filen xaml tilføje to web visninger på siden. Hvis du vil finde, hvor webvisninger indstilles, kan du definerer et gitter med navnet "EngagementGrid", som dog du nødt til at sikre, at der er tilstrækkeligt højde og bredde for to efterfølgende internettet visninger, som du får nu vist meddelelsen, og som i app'en besked om følgende meddelelse:
        
            <Grid x:Name="EngagementGrid"></Grid>

### <a name="i-created-a-push-notificationannouncement-campaign-and-even-after-it-sent-me-the-notification-it-is-showing-as-active-what-does-it-mean"></a>Jeg har oprettet en opslagsnål meddelelse/meddelelse/marketingkampagne, selvom de sendes mig meddelelsen, det der vises som 'Aktiv'. Hvad betyder det? 
**Kampagnens** , du oprettede i Mobile aftale hedder så fordi det er en længerevarende opslagsnål meddelelse betydning, som nye enheder oprette forbindelse til din mobile aftale platform, bliver de automatisk sendt den meddelelse, du konfigurerer her, så længe de opfylder det kriterium, du angiver i kampagnens. Dette er ikke en ét skærmbillede enkelt besked installationsprogrammet. Du skal manuelt Klik på knappen **Udfør** for at afslutte kampagnens, så det ikke sende yderligere meddelelser. 

### <a name="i-created-a-push-campaign-and-i-am-receiving-notifications-successfully-however-whenever-i-open-up-the-app-i-get-the-same-notification-even-when-i-had-actioned-it-before"></a>Jeg har oprettet en opslagsnål kampagnens og jeg modtager meddelelser korrekt men når jeg åbner op app, jeg får meddelelsen til samme selvom jeg havde actioned den før? 
Dette er sandsynligvis sker under test, og hvis du bruger emulatorer eller nogle test framework som TestFlight. Hvad sker der her er på hver app forekomsten, ændres under overførsel af en ny DeviceID, og at sende det til vores backend-version, der forårsager Mobile aftale platformen til at behandle den som en ny enhed og sende meddelelsen. 

## <a name="getting-support"></a>Få Support

Hvis du ikke kan løse problemet kan dig selv og du:

1. Søg efter dit problem i de eksisterende tråde på StackOverflow forum og [MSDN-forum](https://social.msdn.microsoft.com/Forums/windows/en-US/home?forum=azuremobileengagement) , og hvis det er ikke derefter stille et spørgsmål der. 
2. Hvis du finder en funktion, mangler derefter tilføje/stemme for anmodning på vores [UserVoice-forum](https://feedback.azure.com/forums/285737-mobile-engagement/)
3. Hvis du har Microsoft Support åbnet en support-hændelse ved at indsende følgende oplysninger: 
    - Azure abonnement-ID
    - Platform (fx iOS, Android osv)
    - App-ID
    - Kampagnens-ID (For push besked om problemer)
    - Enheds-ID
    - Mobile aftale SDK version (fx Android SDK v2.1.0)
    - Flere oplysninger om fejlen med præcise fejlmeddelelse og scenarie
