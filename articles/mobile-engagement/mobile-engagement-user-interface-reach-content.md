<properties 
   pageTitle="Azure Mobile aftale brugergrænseflade - rækkevidde indhold" 
   description="Lær, hvordan du administrerer entydige indholdet af de forskellige typer opslagsnål besked om kampagner i Azure Mobile aftale" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Sådan administreres entydige indholdet af de forskellige typer opslagsnål meddelelse kampagner
 
Du kan bruge afsnittet indhold i en ny rækkevidde kampagnens til at ændre indholdet af dine meddelelser, forespørgsler, flytter Data og felter (kun Windows Phone). Indstillingen indhold af opslagsnål kampagner er specifikke for typen campaign. 
 
### <a name="content-types"></a>Typer af webstedsindhold:
- Meddelelser
- Afstemninger
- Data skubber
- Felter (kun Windows Phone)
 
## <a name="content-of-announcements"></a>Indholdet af meddelelser
 ![Rækkevidde Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Vælg typen af din meddelelse:
-    Meddelelse om kun: det er en simpel standard meddelelse. Hvilket betyder, der, hvis en bruger klikker på det, vises ingen yderligere visning, men kun den handling, der er knyttet til den skal forekomme.
-    Tekst om Familieforøgelse: det er en meddelelse, der aktiveres, brugeren skal have et kig på en tekstvisning.
-    Web meddelelse: det er en meddelelse, der aktiveres, brugeren skal have et kig på en webvisning.

### <a name="see-also"></a>Se også
- [Når – Sådan gør du-meddelelser][Link 3] 

### <a name="about-web-view-announcements"></a>Om Web visning meddelelser:
Forekomster af mønsteret "{deviceid}" i HTML-kode eller JavaScript-kode, du angiver her, erstattes automatisk med id for den enhed, der viser meddelelsen. Dette er en nem måde at hente Azure Mobile aftale enhed id'er i en ekstern webtjeneste, der er hostet på kontoret tilbage.
Hvis du vil oprette en fuld skærm webvisning (uden handling og Afslut standardknapperne vi giver dig) du kan bruge følgende funktioner fra din web Vis meddelelse JavaScript-kode: 

-    udføre handlingen, meddelelse: ReachContent.actionContent()
-    afslutte fra meddelelsen: ReachContent.exitContent()
 
### <a name="choose-your-action"></a>Vælg din handling:

### <a name="about-action-urls"></a>Om handling URL-adresser:
Alle URL-Adresser, der kan fortolkes af en bestemt enhed operativsystem kan bruges som en handling URL-adresse.
En dedikeret URL-adresse, dit program understøtter muligvis (fx at gøre brugere hoppe til et bestemt skærmbillede) kan også bruges som en handling URL-adresse.
Hver forekomst af {deviceid} mønstret erstattes automatisk af id'et for den enhed, udføre handlingen. Dette kan bruges til at hente nemt Azure Mobile aftale enhed id'er via en ekstern webtjeneste, der er hostet på kontoret tilbage.

- **Android + iOS handlinger**
    - Åbn en webside
    - http://\[web-websted-domæne\] 
    - Eksempel: http://www.azure.com
    - Sende en e-mail
    - mailto:\[e-mail-modtager\]?subject =\[emne\]& brødtekst =\[meddelelse\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Sende en SMS-besked
    - SMS:\[-telefonnummer\] 
    - Eksempel: sms:2125551212
    - Ring til et telefonnummer
    - TEL:\[-telefonnummer\] 
    - Eksempel: tel:2125551212
- **Android kun handlinger**
    - Hente et program på Afspil Store
    - Market://details?id=\[app-pakke\] 
    - Eksempel: market://details?id=com.microsoft.office.word
    - Starte en geografisk-lokaliseret søgning
    - geo:0, 0?q =\[søgeforespørgsel\] 
    - Eksempel: geo:0, 0? q = starbucks, paris
- **iOS kun handlinger**
    - Hente et program på App Store
    - http://iTunes.Apple.com/ [land] /app/ [appnavn på] /id [app-id]? mt = 8 
    - Eksempel: http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Windows-handlinger
    - Åbn en webside
    - http://\[web-websted-domæne\] 
    - Eksempel: http://www.azure.com
    - Sende en e-mail
    - mailto:\[e-mail-modtager\]?subject =\[emne\]& brødtekst =\[meddelelse\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Send en SMS (Skype Store App påkrævet)
    - SMS:\[-telefonnummer\] 
    - Eksempel: sms:2125551212
    - Ring til et telefonnummer (Skype Store App påkrævet)
    - TEL:\[-telefonnummer\] 
    - Eksempel: tel:2125551212
    - Hente et program på Afspil Store
    - MS-windows-store: PDP?PFN =\[app-pakke-ID\] 
    - Eksempel: ms-windows-store: Projektdetaljeside? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Starte en bingmaps søgning
    - bingmaps:?q =\[søgeforespørgsel\] 
    - Eksempel: bingmaps:? q = starbucks, paris
    - Bruge et brugerdefineret farveskema
    - \[brugerdefineret farveskema\]://\[brugerdefineret farveskema parametre\] 
    - Eksempel: myCustomProtocol://myCustomParams
    - Brug af en pakkedata (Store-App for lokalnummer læse påkrævet)
    - \[mappen\]\[data\]. \[udvidelse\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>Oprette en sporing URL-adresse:
-    Se afsnittet "Indstillinger" i den <UI Documentation> for instruktion på at opbygge en sporing URL-adresse, så brugerne kan hente en af dine andre programmer.
 
### <a name="define-the-texts-of-your-announcement"></a>Definere teksten til din meddelelse
Skriv den titel, indhold og knappen teksten til din meddelelse. Du kan målrette en målgruppe af en fremtidig kampagnens baseret på rækkevidde feedback over, hvordan brugere svaret til denne campaign. Målretning til målgruppe kan være baseret på feedback på om denne kampagnens blev lige rykkes, besvaret, actioned eller afsluttet.

### <a name="see-also"></a>Se også
- [Brugergrænsefladen dokumentation - rækkevidde - nyt opslagsnål kriterium][Link 28]

## <a name="content-of-polls"></a>Indholdet af afstemninger
![Rækkevidde Content2][31] Udfyld den titel, beskrivelse og knappen teksten til din meddelelse. Tilføj derefter spørgsmål og valgmuligheder for at få svar på dine spørgsmål.
Du kan målrette en målgruppe af en fremtidig kampagnens baseret på rækkevidde feedback over, hvordan brugere svaret til denne campaign. Målretning til målgruppe kan være baseret på om denne kampagnens blev lige rykkes, besvaret, actioned eller afsluttet. Målretning til målgruppe kan også være baseret på afstemning answer feedback, hvor spørgsmål og svar valg anvendes som kriterier.

### <a name="see-also"></a>Se også
- [Brugergrænsefladen dokumentation - rækkevidde - nyt opslagsnål kriterium][Link 28]
 
## <a name="content-of-data-pushes"></a>Indholdet af Data skubber
![Rækkevidde Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Vælg typen af dine data:
- Tekst
- Binære data
- Base64-data

### <a name="define-the-content-of-your-data"></a>Definere indholdet af dine data
- Hvis du har valgt for at skubbe tekstdata, kopiere og indsætte teksten i feltet "indhold".
- Hvis du har valgt for at skubbe binære eller base64 data, kan du bruge knappen "Overfør din fil" til at overføre din fil.
- Du kan målrette en målgruppe af en fremtidig kampagnens baseret på rækkevidde feedback over, hvordan brugere svaret til denne campaign. Målretning til målgruppe kan være baseret på om denne kampagnens blev lige rykkes, besvaret, actioned eller afsluttet.

### <a name="see-also"></a>Se også
- [Brugergrænsefladen dokumentation - rækkevidde - nyt opslagsnål kriterium][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Indholdet af felter (kun Windows Phone)
![Rækkevidde Content4][33]

### <a name="define-the-content-of-your-tile"></a>Definere indholdet af dit felt
Feltet data er teksten, der skal vises i feltet af din app på Windows Phone enheder.
Et felt push er den Microsoft opslagsnål meddelelse Service (MPNS) version af en oprindelig pushbeskeder for Windows Phone. Feltet opslagsnål typen er den eneste push-type, der ikke har et svar, og så publikum på mellem fremtidige kampagner kan ikke bygget på resultatet af et felt opslagsnål campaign. 

### <a name="see-also"></a>Se også
- [API dokumentation - rækkevidde API - oprindelig opslagsnål][Link 4]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
