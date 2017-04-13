<properties 
   pageTitle="Azure Mobile aftale brugergrænseflade - rækkevidde Sådan"
   description="Oversigt over brugergrænsefladen af brugerkonti til Azure Mobile aftale" 
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

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Hvordan du kommer i gang bruge og administrere flytter til Henvend dig til dine slutbrugere

Når SDK er fuldt integreret i din app, du kan komme i gang ved hjælp af den sektionen rækkevidde af Brugergrænsefladen til at pushmeddelelser til brugerne af din app.  

## <a name="do-your-first-push-notification-campaign"></a>Gør din første opslagsnål meddelelse kampagnens
-    Bekræft, at din rækkevidde er integreret i din app med SDK. 
-    Vælg dit program
 
![First1][1]

-    Gå til den "Rækkevidde" sektion og klikke på "ny meddelelse"
 
![First2][2]

-    Oprette en ny kampagnens, og kald den
 
 ![First3][3]

-    Vælg, hvordan meddelelsen skal sendes, som i app'en kun
 
![First4][4]

-    Oprette den meddelelse, du vil push
 
![First5][5]

-    Du kan skrive en titel på meddelelse (valgfrit).
-    Skriv opslagsnål meddelelsesindhold.
-    Du kan overføre et billede. Vær opmærksom på, at størrelsen på filen ikke må overstige 32.768 byte.
-    Du har også muligheden for at vælge flere indstillinger, men ved fokus i dette selvstudium, vi ser, der senere.

-    Vælg den indholdstype som meddelelse kun
 
![First6][6]

-    Oprette din opslagsnål kampagnens og det vises i listen over kampagnens.
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>Teste din opslagsnål meddelelse kampagnens
![Test1][8]

-    Registrere din enhed.
-    Klik på afkrydsningsfeltet for den enhed, du vil push.
-    Klik på knappen "Test" for at sende opslagsnål til enheden.
 
![Test2][9]

-    Aktivere kampagnens
 
![Test3][10]

-    Nu, hvor du har oprettet din kampagnens skal du blot aktivere den til meddelelsen kan installeres til brugerne.
 
## <a name="send-personalized-pushes"></a>Send personlige skubber
-    I dette eksempel opretter en opslagsnål, hvor der er angivet en brugerdefineret rabatkode i push-besked.
 
![Personalize1][11]

Tilpasning fungerer ved at erstatte et mærke ved fra en app oplysninger mærke så, har du at sikre, at brugeren har de korrekte app-oplysninger, som defineret først. I dette eksempel har målrettede brugere en app oplysninger-kode med navnet rebate_code, der er defineret.
Som du se over indeholder indholdet i meddelelsen opslagsnål markøren ${rebate_code} som indikerer, at det er at blive erstattet af det faktiske indhold af mærket app oplysninger.

> Advarsel: Hvis mærket app oplysninger ikke er defineret for brugeren, brugeren ikke modtager tryk.

-    Resultat
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Du kan yderligere tilpasse teksten din meddelelse
![Personalize3][13]

-    Herunder titlen på beskeden,
-    Og indholdet af meddelelsen.
-    Vælg typen meddelelse (tekstvisning eller webvisning)
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Også kan tilpassede brødteksten i en meddelelse med:
-    URL-adressen handling skal du have til at tilpasse landingssiden
-    Titlen,
-    Meddelelsens brødtekst.
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Skelne din Push meddelelse (ind eller ud på app)
-    Vælg typen meddelelse, du vil push, Vælg dit program, gå til afsnittet "Når", vælge eller oprette en opslagsnål kampagnens og gå til afsnittet "Meddelelse".
 
-    Klik på "levering tilstand" ønskede.
-    Klik på afkrydsningsfeltet "Begræns aktiviteter", når du vil meddelelsen opstår på bestemte aktiviteter (skærme).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>"Ikke kun App" leveringstilstand
![Differentiate2][16]

"Ikke kun App" leveringstilstand giver opslagsnål besked, når programmet er lukket. Dette er standard opslagsnål meddelelsen.
Når du vælger "ikke kun app", du i forvejen have angivet certifikater fra platformen, er at opbygge dit program på (APNS eller GCM).

### <a name="see-also"></a>Se også
-  [Apple-Pushmeddelelsestjeneste – certifikater](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud Messaging-certifikat] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"i app'en kun" delivery-tilstand
![Differentiate3][17]

"I app'en kun" leveringstilstand giver opslagsnål besked, når programmet kører.
For denne meddelelse behøver du ikke at gå gennem APNS og GCM systemet.
Du kan bruge i app'en levering systemet for at nå dine slutbrugere.
Du kan fuldt tilpasse meddelelsen og Beslut, hvor aktivitet (skærm) meddelelsen vises.

### <a name="anytime-delivery-mode"></a>"Når som helst" leveringstilstand
Du kan vælge en "Når som helst" leveringstilstand, sikrer, at du når dine slutbrugere, om programmet kører eller ej.
Når du vælger "Når som helst", du i forvejen have angivet certifikater fra platformen, er at opbygge dit program på (APNS eller GCM). 
 
## <a name="schedule-a-push-campaign"></a>Planlægge en opslagsnål kampagnens
### <a name="plan-to-start-a-campaign"></a>Planlægge at starte en kampagnens
![Shedule1][18]

Det er 21 af marts, og du har en meddelelse til at foretage og planlagt for 22. af marts ved midnat. Du behøver ikke at holde foran grænsefladen til at gøre en opslagsnål! Du kan planlægge på forhånd, nøjagtige minuttet meddelelser vil blive sendt.
-    Fjern markeringen "Ingen" afkrydsningsfeltet, og vælg et starttidspunkt 
-    Vælg datoen og det klokkeslæt, du vil starte opslagsnål campaign.

### <a name="plan-to-end-a-campaign"></a>Planlægge at afslutte en kampagnens
![Shedule2][19]

Du vil din kampagnens skal stoppe på 25 af marts ved 3. kl pm, men du kender du ikke er tilgængelig til at gøre det.
Du behøver ikke at holde foran grænsefladen til push! Du kan planlægge på forhånd, nøjagtige minuttet din kampagnens stopper.
-    Klik på "Ingen" afkrydsningsfeltet eller Vælg et sluttidspunkt
-    Vælg datoen og det klokkeslæt, du vil færdiggøre opslagsnål campaign.

### <a name="end-a-campaign-manually"></a>Afslutte en kampagnens manuelt
![Shedule3][20]

Som standard, "Ingen"-afkrydsningsfelterne er markeret.
Det betyder, at kampagnens starter, så snart du aktivere den i sektionen rækkevidde og afsluttes, når du stopper i afsnittet rækkevidde.
 
> Bemærk: Kampagner, der er oprettet uden en slutdato for gemme tryk lokalt på enheden, og Vis den næste gang appen åbnes, selvom kampagnens afbrydes manuelt.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Forbedre en Push-meddelelse med en tekstvisning
### <a name="what-is-a-text-view"></a>Hvad er en tekst-visning?
![TextView1][21]

En tekstvisning er en pop op-vindue med tekstindhold. Denne pop op-vindue vises, når slutbrugeren har klikket på opslagsnål meddelelsen.
Du kan bruge en tekstvisning til at præsentere mere indhold til dine slutbrugere. Dette er også mulighed for at præsentere et kald til handling som springe til en side af din app, omdirigere til en butik, åbner en webside, sende en e-mail, starte en geografisk-lokaliseret søgning osv...

### <a name="example-text-view"></a>Eksempel: tekstvisning
-    Oprette din opslagsnål meddelelse kampagnens i afsnittet "Rækkevidde" og navngive din kampagnens
 
![TextView2][22]

-    Skriv den meddelelse, der skal vises på meddelelsen.
-    Vælg den meddelelse indholdstype for "tekst"
 
![TextView3][23]

> Bemærk: Når du klikker en tekstvisning, den altid leveres med en meddelelse først. 

- Definere teksten (når du har valgt tekst om Familieforøgelse indholdet, sektionen underordnede vises, så du kan definere den tekst, du vil have vist.)
 
![TextView4][24]

-    Skriv den titel, der vises øverst i meddelelsen.
-    Skriv hovedindholdet af visningen tekst.
-    Skriv det indhold, der skal vises på handlingsknappen (en handlingsknap gør det muligt for programmet for at gøre en bestemt handling, som åbner en side med det program, omdirigere til en App store eller alle slags kilder, du kan angive).
-    Skrive det indhold, der skal vises på knappen Afslut (ved at klikke på knappen Afslut, visningen tekst forsvinder.)
 
-    Oprette din opslagsnål meddelelse kampagnens og den vises på listen campaign.
 
![TextView5][25]

-    Aktivere din opslagsnål meddelelse kampagnens for at sende visningen tekst til dine brugere.
 
![TextView6][26]

-    Resultat
 
![TextView7][27]

-    Brugeren modtager meddelelsen og klik på den.
-    Visningen tekst vises som en pop op-vindue, så brugeren kan interagere med den.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Forbedre en Push-meddelelse med en webvisning
### <a name="what-is-a-web-view"></a>Hvad er en webvisning?
![WebView1][28]

En webvisning er en pop op-vindue med webindhold. Denne pop op-vindue vises, når slutbrugeren har klikket på opslagsnål meddelelsen.
En webvisning kan du have flere interaktion med slutbrugeren.
Dette er også mulighed for at præsentere et kald til handling som omdirigering til App Store, åbne en webside, afsendelse af en meddelelse, skal du starte en geografisk-lokaliseret søgning: osv...

### <a name="example-web-view"></a>Eksempel: Webvisning
-    Oprette din opslagsnål kampagnens i afsnittet "Rækkevidde" og navngive din campaign.
 
![WebView2][29]

-    Skriv den meddelelse, der skal vises på meddelelsen.
-    Vælg den meddelelse indholdstype som "web"
 
![WebView3][30]

### <a name="about-announcement-types"></a>Om meddelelse typer:
- Meddelelse om kun: det er en simpel standard meddelelse. Hvilket betyder, der, hvis en bruger klikker på det, vises ingen yderligere visning, men kun den handling, der er knyttet til den skal forekomme.
- Tekst om Familieforøgelse: det er en meddelelse, der aktiveres, brugeren skal have et kig på en tekstvisning.
- Web meddelelse: det er en meddelelse, der aktiveres, brugeren skal have et kig på en webvisning.
Vælg indholdet, "Web meddelelsen".

> Bemærk: Når du klikker en webvisning, den altid leveres med en meddelelse først.

- Definere webindhold (når du har valgt meddelelse webindhold, underafsnittet vises, så du kan definere webindhold visning du vil have vist.)

 
![WebView4][31]

-    Skriv den titel, der vises øverst i meddelelsen (valgfrit).
-    Skriv din HTML-kode her.
-    Klik på den kilde, redigering tilstandsknap for at skifte edition og se, hvordan det ser ud.
-    Skriv det indhold, der skal vises på handlingsknappen (en handlingsknap gør det muligt for programmet for at gøre en bestemt handling, som åbner en side med det program, omdirigere til en butik eller alle slags kilder, du kan angive).
-    Skrive det indhold, der skal vises på knappen Afslut (ved at klikke på knappen Afslut, webvisning forsvinder).
 
-    Resultat
 
![WebView5][32]

-    Brugeren modtager meddelelsen, og klik på den.
-    Visningen tekst vises som en pop op-vindue, så brugeren kan interagere med den.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
