<properties 
   pageTitle="Azure Mobile aftale brugergrænseflade - rækkevidde kampagnens" 
   description="Laern hvordan kampagner, oprette og administrere opslagsnål meddelelse som ved hjælp af Azure Mobile aftale" 
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


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Hvordan du kan oprette og administrere opslagsnål meddelelse kampagner
Du kan bruge sektionen rækkevidde af Brugergrænsefladen til at oprette en ny opslagsnål kampagnens med en kompleks formel ved at give alle de oplysninger, du vil sende en besked om opslagsnål. Indstillingerne i en opslagsnål kampagnens variere en smule, afhængigt af de fire måder: meddelelser, forespørgsler, flytter Data og felter (kun Windows Phone).

### <a name="option-applies-to"></a>Indstillingen gælder for:
- Sprog: Alle (meddelelser, afstemninger, Data skubber, felter)
- Kampagnens: Alle (meddelelser, afstemninger, Data skubber, felter)
- Meddelelse: Meddelelser, afstemninger
- Content: Entydige for hver campaign type
- Målgruppe: Alle (meddelelser, afstemninger, Data skubber, felter)
- Tidsramme: meddelelser, forespørgsler, felter
- Test: Alle (meddelelser, afstemninger, Data skubber, felter)
 
![Rækkevidde Campaign1][20]

## <a name="languages"></a>Sprog
Du kan bruge rullemenuen sprog til at sende en anden version af din opslagsnål til enheder, der er angivet til brug af forskellige sprog. Som standard alle enheder, modtager den samme opslagsnål uanset hvilket sprog, de er indstillet til brug. Brugere med deres enhed, der er angivet til et andet sprog, modtager standard sprogversionen af tryk. Mange af opslagsnål kampagnens indstillinger kan du angive alternative indhold for hvert af de ekstra sprog, du vælger. 
 
![Rækkevidde Campaign2][21]

### <a name="language-differences-apply-to"></a>Sprogforskelle gælder for:
- Sprog: Entydige sprog kan vælges ud over standardsproget
- Kampagnens: Ens for alle sprog
- Meddelelse: Entydige for de enkelte sprog ud over standardsproget
- Content: Entydige for de enkelte sprog ud over standardsproget
- Målgruppe: Filtreres ved hjælp af et separat sprog kriterium
- Tidsramme: samme for alle sprog
- Test: Kan blive sendt til de enkelte sprog ad gangen
 
### <a name="supported-languages"></a>Understøttede sprog:
- Arabisk (ar) 
- Bulgarsk (bg) 
- Katalansk (ca) 
- Kinesisk (zh) 
- Kroatisk (t) 
- Czech (CS) 
- Dansk (da) 
- Nederlandsk (nl) 
- Engelsk (da) 
- Finsk (fi) 
- Fransk (f) 
- Tysk (de) 
- Græsk (e1) 
- Hebraisk (han) 
- Hindi (høj) 
- Ungarsk (hu) 
- Indonesisk (id) 
- Italiensk (det) 
- Japansk (ja) 
- Koreansk (ko) 
- Lettisk (lv) 
- Litauisk (lt) 
- Malaysisk (macrolanguage) (ms) 
- Norsk (bokmål) (nb) 
- Polsk (pl) 
- Portugisisk (pt) 
- Rumænsk (ro) 
- Russisk (ru) 
- Serbisk (sr) 
- Slovakisk (sk) 
- Slovensk (sl) 
- Spansk (es) 
- Svensk (sv) 
- Tagalog (tl) 
- Thai (de) 
- Tyrkisk (tr) 
- Ukrainsk (Storbritannien) 
- Vietnamesisk (vi) 
 
## <a name="campaign"></a>Kampagnens
Du kan bruge sektionen kampagnens til at angive navn og kategori af din kampagnens samt, som hvis du planlægger at ignorere afsnittet målgruppe i en opslagsnål kampagnens og sende denne kampagnens via når API (og alle elementer med lav niveauet Push-API) i stedet. Kategorier kan bruges med en speciel meddelelsesskabelon til kontrolelementet i app-meddelelser baseret på foruddefinerede indstillinger. Du kan få en liste over dine eksisterende "kategorier" via API når.

> Advarsel: Hvis du bruger indstillingen "Ignorer målgruppe opslagsnål sendes til brugere via API" i afsnittet "Kampagnens" i en rækkevidde kampagnens kampagnens sender ikke automatisk, skal du sende den via API når manuelt.
 
![Rækkevidde Campaign3][22]
 
### <a name="option-applies-to"></a>Indstillingen gælder for:
- Navn: alle
- Kategori: Meddelelser, afstemninger
- Ignorere målgruppe, opslagsnål sendes til brugere via API: alle
 
## <a name="notification"></a>Meddelelse om
Du kan bruge afsnittet besked til at angive grundlæggende indstillinger for din ordre, herunder: titlen på opslagsnål, meddelelsen, et billede i app'en, eller hvis det er dismissible. Indstillinger for mange meddelelser er specifikke for platform på din enhed. Du kan vælge, om din opslagsnål sendes "i app" eller "af app" eller begge dele. (Husk, at brugere kan "vælge i" eller "fravælge" "af app" skubber på operativsystemet niveau på deres enheder, og Azure Mobile aftale vil ikke kunne tilsidesætte denne indstilling. Husk også, når API håndterer "i app" og "forældet app" skubber. Push-API kan bruges til at håndtere "af app" skubber for.) Skubber kan tilpasses med billeder eller HTML-indhold, herunder deep links til at sammenkæde uden for din App eller til en anden placering i din App (Android SDK 2.1.0 eller nyere bruges administrationssiden kategorier, der kræves). Du kan ændre ikonet eller iOS-badge og sende tekst eller web indhold (en pop op-vindue med HTML-indhold, URL-hyperlink til en anden placering i eller uden for app). Du kan også gøre Ring til Android-enheder eller vibrationer tryk. (Husk, at du skal den korrekte SDK tilladelser i din Android manifestet filen for at ringe eller vibrationer en enhed.) Der findes i øjeblikket ingen branche standarden for Android "overblik" størrelser, da skærmstørrelser er anderledes ud på alle enheder, men 400 x 100 billeder arbejde på næsten alle skærmstørrelse.

### <a name="delivery-types"></a>Typer af meddelelseslevering:
-    Af app'en kun: meddelelsen vil blive leveret, når brugeren ikke bruge programmet.
- Forældet kun appmeddelelse kræver et certifikat fra Apple eller Google (APNS eller GCM certifikat).
- I app'en kun: meddelelsen vises kun, når programmet kører.
- Meddelelsen bruger Capptain levering filsystemet når brugeren. Fuldt kan du tilpasse visuelle layout/visningen af din ordre.
- Når som helst: Denne indstilling sikrer, at du sender en meddelelse, enten programmet kører eller ej.

 
![Rækkevidde Campaign4][23]

### <a name="option-applies-to"></a>Indstillingen gælder for:
- Meddelelse: Meddelelser, afstemninger
 
## <a name="content"></a>Indhold
Du kan bruge afsnittet indhold til at ændre indholdet af dine meddelelser, forespørgsler, flytter Data og felter (kun Windows Phone). Indstillingen indhold af opslagsnål kampagner er specifikke for typen campaign. 

### <a name="see-also"></a>Se også
- [Brugergrænsefladen dokumentation - når - Push-indhold][Link 29]
 
![Rækkevidde Campaign5][24]

## <a name="audience"></a>Målgruppe
Du kan bruge sektionen målgruppe for at definere en almindelig liste over elementer til at begrænse din kampagnens eller begrænsninger for din kampagnens baseret på brugerdefinerede kriterier. Det standardsæt af indstillinger for at begrænse målgruppen kan du push til nye og gamle brugere eller kun oprindelige push-brugere. Du kan også angive en kvote for at begrænse antallet af brugere, der modtager tryk. Du kan manuelt redigere udtrykket for hvordan din kampagnens er filtreret til at medtage en eller flere kriterium til destinationen brugere. Manuelt kan du skrive et udtryk, der målgruppe. Sådan et udtryk skal eksplicit definere relationen mellem kriterier. Et kriterium er beskrevet i et id, der skal starte med et stort bogstav og må ikke indeholde mellemrum. Forholdet mellem kriterierne, der kan være beskrevet ved at bruge 'og', 'eller', 'ikke' operatorer samt '(',')'. Eksempel: "Criterion1 eller (Criterion1 og ikke Criterion2)".

> Bemærk: Med et stort antal deltagere inkluderet i kampagner, serversiden målretning af scanning kan være langsom, især hvis du forsøger at starte flere kampagner på samme tid.

- Hvis det er muligt, starte kun én kampagnens ad gangen.
- På det mest kun starte fire kampagner ad gangen.
- Push kun til dit aktive brugere (afkrydsningsfeltet "deltage kun brugere, der kan ringes ved hjælp af Oprindelig opslagsnål" og "Deltage kun aktive brugere") så kun de brugere, der stadig appen er installeret og bruge det skal være scannet.
Når målgruppen er defineret, kan du bruge knappen simulate til at finde ud af, hvor mange brugere modtager denne opslagsnål. Dette vil beregne antallet af kendte brugere potentielt målet for målgruppen (dette er en vurdering, der er baseret på en stikprøve af brugere). Vær opmærksom på, at brugere, der har fjernet programmet er også en del af målgruppen, men kan ikke kontaktes.

### <a name="see-also"></a>Se også
- [Brugergrænsefladen dokumentation - rækkevidde - nyt opslagsnål kriterium][Link 28]

![Rækkevidde Campaign6][25]

### <a name="edit-expression"></a>Redigere udtryk
![Rækkevidde Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>Grænse for din målgruppe indstilling gælder for:
- Deltage kun et undersæt af brugere: alle (meddelelser afstemninger flytter Data, og felter)
- Deltage kun gamle brugere: alle (meddelelser afstemninger flytter Data, og felter)
- Deltage kun nye brugere: alle (meddelelser afstemninger flytter Data, og felter)
- Deltage kun inaktiv brugere: meddelelser, forespørgsler, felter
- Deltage kun aktive brugere: alle (meddelelser afstemninger flytter Data, og felter)
- Deltage kun brugere, der kan ringes ved hjælp af Oprindelig Push: meddelelser, afstemninger
 
## <a name="time-frame"></a>Tidsramme
Du kan bruge sektionen tidsramme til at angive, når opslagsnål vil blive sendt, eller du kan lade tidsrammen tomt for at starte kampagnens med det samme. Husk, at ved hjælp af den slutbrugere tidszone kan starte kampagnens en dag tidligere, end du kan forvente for din slutbrugerne i Asien og sende mindre grupper af skubber ad gangen, indtil alle tidszoner i verden svarer til den tidsramme, der er angivet for din campaign. Ved hjælp af den slutbrugere tidszone kan også medføre forsinkelser i-kampagner, da den har til at anmode om tiden fra telefonen, før du starter på opslagsnål.

> Bemærk: Kampagner uden en slutdato kan gemme i cachen skubber lokalt og stadig få dem vist efter at du manuelt fuldført kampagner. For at undgå dette problem, specifikke et sluttidspunkt for kampagner.

### <a name="see-also"></a>Se også
- [Når – Sådan gør du-vejledninger – planlægning][Link 3] 
 
![Rækkevidde Campaign8][27]

### <a name="settings-apply-to"></a>Indstillingerne gælder for:
- Tidsramme: meddelelser, forespørgsler, felter
 
## <a name="test"></a>Test
Du kan bruge afsnittet Test til at sende denne opslagsnål til din egen test enhed før du gemmer kampagnens. Hvis du har konfigureret et brugerdefineret sprog for denne kampagnens, kan du teste tryk i de enkelte sprog. Du kan konfigurere en test enhed fra "Min konto".
> Bemærk: Ingen data logføres, når du bruger knappen "teste" serversiden skubber, data logføres kun for reelle opslagsnål kampagner.

### <a name="see-also"></a>Se også
- [Brugergrænsefladen dokumentation - min konto][Link 14]
 
![Rækkevidde Campaign9][28]

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
 
