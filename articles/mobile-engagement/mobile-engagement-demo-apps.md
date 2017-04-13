<properties
    pageTitle="Azure Mobile aftale demo app | Microsoft Azure"
    description="I denne artikel beskrives, hvor du kan hente, hvordan du bruger og fordelene ved at bruge Azure Mobile aftale demo app"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/10/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile aftale demo-app

Vi har udgivet en Azure Mobile aftale demo app til **iOS** **Android**og **Windows** -platforme kan hjælpe dig med at finde nyttige ressourcer og yderligere oplysninger om Mobile aftale.

Appen hjælper dig med at:

- Nemt finde nyttige links til Mobile aftale ressourcer som videoer, dokumentation, forummet for support og Sådan kommer du øger anmode om funktioner.
- Eksempel på oplevelse meddelelser, der understøttes af Mobile aftale til at få ideer til din egen mobilprogrammer.
- Bruge en referenceimplementering til at undersøge, hvordan du kan implementere Mobile aftale i din egen app. Du kan lære at:

    - Indsamle analytics-data.
    - Implementere avanceret meddelelse scenarier typer som *intramuskulært fuldskærmsvisning* eller *genvejsmenuen*.
    - Implementere undersøgelser og afstemninger.
    - Implementere uovervåget push-data og opslagsnål scenarier.   

## <a name="app-installation"></a>App-installation
Denne app er tilgængelig i de følgende app gemmer:

- **Windows Universal demo app**:

    - Hente app'en på [Windows-App gemme](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
    - Appen blev udviklet som en Windows 10 Universal app. Koden er tilgængelig på [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).

- **iOS demo app**:

    - Hente appen på [Apple gemme](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
    - Appen blev udviklet i iOS Swift. Koden er tilgængelig på [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).

- **Android demo app**:

    - Hente appen på [Google Play gemme](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
    - Koden er tilgængelig på [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Windows Universal demo-app][1]

![iOS demo app][2]
![Android demo app][3]


## <a name="usage"></a>Brugen

Du kan bruge denne app på følgende måder:

**Hent appen på din enhed fra programmet store hyperlinkene (tidligere):**

>[AZURE.IMPORTANT] Du behøver ikke en Azure-konto eller har du brug for at oprette forbindelse appen til back-end. Appen fungerer uafhængigt.

- Når du har appen på din enhed, kan du gå gennem linkene i venstre menu for at finde nyttige ressourcer om Mobile aftale.
- Vi har tilføjet [tjenestens RSS-feed](https://aka.ms/azmerssfeed) i dette program, så du altid er opdateret om de seneste opdateringer.
- Du kan også gå igennem meddelelse eksempelscenarier at føle, at typer meddelelser, der understøttes af Mobile aftale for hver platform. Disse meddelelser kan opleves lokalt –, der er, kan du klikke på knapperne på på skærmen for at få vist meddelelser oplevelse, der er identisk med at sende meddelelserne fra den Mobile aftale platform.

![Menuen i App til Windows][4]

![Appmenuen til iOS][5]
![appmenuen til Android][6]

**Hent kildekoden fra GitHub hyperlinkene (tidligere):**

- Når du har downloadet koden, skal du åbne den i de respektive udviklingsmiljø – XCode til iOS, Android Studio til Android og Visual Studio til Windows.
- Derefter skal du følge vores [grundlæggende trin til integration af SDK](mobile-engagement-windows-store-dotnet-get-started.md) , så det lykkedes at oprette forbindelse denne app til sin egen Mobile aftale back end-forekomst.
    - Du skal konfigurere en forbindelsesstreng i appen.
    - Du skal også konfigurere opslagsnål meddelelse platform til din app.
- Du vil bemærke, at selve appen er udstyret med Mobile aftale. Derfor, som du åbner appen efter at slutte den til back-end, du vil kunne se brugersession, aktiviteter, begivenheder og osv., under fanen **skærm** .
- Du vil også kunne sende meddelelser til denne app fra din egen Mobile aftale forekomst, i stedet for ved hjælp af lokale meddelelser.
    - Her kan du tilføje din enhed som en test enhed ved hjælp af menupunktet **få enheds-ID** i appen. Det giver dig et enheds-ID, som du derefter registrerer som en test-enhed med din platform back end-forekomst.

    ![Enheds-ID i Windows][7]

    ![Enheds-ID på iOS][8]
    ![enheds-ID på Android-enhed][9]

## <a name="key-features-of-the-demo-app"></a>Vigtigste funktioner i appen demo

- Som nævnt tidligere, med denne app, har du alle de ressourcer, der er vigtige for Mobile aftale i hånden. Du kan gå gennem linkene på menuen til venstre.

- Du kan opleve ud i app beskeder for hver platform. Disse meddelelser kan leveres, som **kun meddelelse**, hvor at klikke på meddelelsen blot åbner en oprindelig skærmbillede af programmet op (ved hjælp af **deep sammenkædning**) – eller som en **Web meddelelse**, hvor du kan afholde yderligere HTML-indhold fra Mobile aftale back-end skal vises, når der klikkes på meddelelsen.

    ![Ud over app-meddelelser][29]

- IOS har du at lukke appen for at få vist pushmeddelelser ud i app eller system. Du kan se på implementeringen her for at tilføje **handlingsknapper**, som dem, der er føjet til denne ud i app meddelelse til *Feedback* og *del* (så brugeren kan tage handling direkte fra meddelelsen til selve).

    ![Meddelelser om ud i app på iOS][11] ![Ud over appmeddelelse vises på iOS][14]

- På Android-enhed tilføjer de indstillinger, der understøttes flere linjer tekst (**Stor tekst**) eller en meddelelse om billede (**Overblik**) på meddelelsen, sammen med **handlingsknapper** (som understøttes af iOS).

    ![Ud over app-meddelelser på Android-enhed][12] ![Ud over appmeddelelse vises på Android][15]

- I Windows 10, kan du se, hvordan meddelelserne ser ud på PC'EN. Denne meddelelse vises også i Windows 10 **Meddelelse Center**. Der er ingen understøttelse for at tilføje **handlingsknapper** i øjeblikket i Windows SDK.

    ![Ud over app-meddelelser i Windows][10] ![Ud over app-visning i Windows][13]

- Du kan opleve standard "i app"-meddelelser for hver platform. Dette er en to-trins oplevelse, hvor en **Chatmeddelelses** -vinduet vises først. Når du klikker på den, åbnes af en fuld skærm **meddelelse**, som vist på følgende skærmbillede. Indholdet af denne meddelelse kommer fra din Mobile aftale back end-forekomst. SDK indeholder skabeloner til både meddelelser og meddelelser. Du kan nemt tilpasse dem, som vist i denne demo app med tilføjelse af vores logo og farvelægning.  

    ![I app-meddelelser i Windows][16]

    ![I app-meddelelser på iOS][17]  ![I app-meddelelser på Android-enhed][18]

    **iOS**, **Android**

- Du kan også bruge funktionen **kategori** i Mobile aftale til at tilpasse denne standard oplevelse. I app demo har vi vist to almindelige måder at ændre opleve i meddelelser. Bemærk, at funktionen ikke er endnu understøttet i Windows SDK.

    **Fuld skærmvisning intramuskulært:**

    ![Meddelelse i app - intramuskulært kategori][30]

    ![Intramuskulært kategori på iOS][21]  ![Intramuskulært kategori på Android-enhed][22]

    **Pop op-meddelelser:**

    ![Meddelelse i app - pop op-kategori][31]

    ![Pop op-meddelelser på iOS][19]   ![Pop op-meddelelser på Android-enhed][20]

**iOS**, **Android**

- Mobile aftale understøtter også en særlig slags i app'en meddelelse kaldet **afstemninger**. Dette kan du sende hurtig undersøgelser til brugerne segmenteret app. Du kan tilføje spørgsmål og indstillinger for hvert spørgsmål som følgende skærmbillede. Dette vil få vist som en i-appmeddelelse til app-bruger.   

    ![Afstemning beskeder][32]

    ![Undersøgelse i Windows][26]

    ![Undersøgelse på iOS][27]   ![Undersøgelse på Android-enhed][28]

**iOS**, **Android**

- Mobile aftale understøtter også at sende uovervåget **Data Push** -beskeder. Med disse meddelelser, du kan sende data fra din tjeneste (såsom JSON dataene i følgende eksempel), som du kan håndtere i din app og udføre en handling. Et eksempel er, hvordan vi vil ændre prisen på et element ved hjælp af data pushmeddelelser selektivt.

    ![Data opslagsnål meddelelse][33]

    ![Data opslagsnål meddelelser i Windows][23]

    ![Data opslagsnål besked om på iOS][24]  ![Data opslagsnål meddelelse på Android-enhed][25]

**iOS**, **Android**

> [AZURE.NOTE] Du kan få vist detaljerede trinvise instruktioner til en af disse meddelelser ved at klikke på **Klik her for at få oplysninger om, hvordan du kan sende disse meddelelser fra Mobile aftale platform** på en hvilken som helst skærmbillede med eksempel på meddelelse.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
