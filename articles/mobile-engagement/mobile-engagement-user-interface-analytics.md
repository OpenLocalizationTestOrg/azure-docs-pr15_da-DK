<properties
   pageTitle="Azure Mobile aftale brugergrænseflade - analyser"
   description="Lær at analysere historiske data om dit program ved hjælp af Azure Mobile aftale"
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

# <a name="how-to-analyze-historical-data-about-your-application"></a>Hvordan til at analysere historiske data om dit program

I denne artikel beskrives fanen **analyser** af portalen **Mobile aftale** . Du kan bruge portalen **Mobile aftale** til at overvåge og administrere dine mobile-apps. Bemærk, at for at starte ved hjælp af portalen skal du først oprette en **Aftale til Azure Mobile** -konto.


Sektionen analyser af Brugergrænsefladen indeholder aggregeret oplysninger om dit program, der er baseret på historiske data, der opdateres døgnet. Oplysninger, der vises på forskellige dashboards bestående af linje/søjleudsnit diagrammer, gitre og kort. Dataene kan også downloades som .csv-filer. De fleste af de samme oplysninger er tilgængelige i realtid i afsnittet skærm i Brugergrænsefladen, og det kan også åbnes fra Analytics API.

>[AZURE.NOTE] Mange sektioner af portalen **Mobile aftale** Brugergrænsefladen indeholder knappen **Vis Hjælp** . Tryk på denne knap for at få flere kontekstafhængige oplysninger om en sektion.

## <a name="standard-and-custom-analytics"></a>Standardfarver og brugerdefinerede Analytics

Azure Mobile aftale indeholder et sæt af grundlæggende, standard analytisk oplysninger om dine programmer, kan afbildes, så snart du integrerer din app med SDK. Azure Mobile aftale giver også mulighed for at indsamle yderligere brugerdefinerede analyser oplysninger, du vil om dine slutbrugere funktionsmåde. Du kan gøre dette ved at oprette et mærke plan over brugerdefinerede "mærker (app oplysninger)", oprettet i **indstillingerne** , så Azure Mobile aftale kan indsamle disse yderligere oplysninger for dig.



## <a name="analytics"></a>Analyser
- Dashboard: Viser generelle oplysninger om nye og aktiverer brugerne og deres tendenser.
- Brugere: Brugere er identificeret med deres enheds-id: denne id er entydige for hver enhed (en ny bruger er faktisk én nye enhed). En bruger betragtes som ny på et bestemt tidsinterval, hvis han har udført sin første session i dette tidsinterval. En bruger betragtes som bevares hvis han har udført mindst én session de seneste 7 dage. Aktive brugere er brugere, der foretages mindst én session i en given periode. Du kan sortere efter, månedligt, ugentligt, dagligt eller hver time perioder. Alle diagrammerne ligner, men giver dig mulighed for at filtrere efter forskellige funktioner, som versionen af dit program, og derefter på Sortér efter et bestemt tidsrum. Standardoplysninger indsamlede ved at integrere SDK omfatter følgende: aktive brugere, ny bruger, antallet af sessioner, længden af hver enkelt session, tekniske oplysninger om land, lokale variabler, placering, sprog carrier, enheder, firmware, netværk (Wi-Fi), versioner af app og SDK, som bruges af kunder. Disse oplysninger kan ses i realtid i afsnittet skærm.

> Bemærk: Tidsperioden er baseret på datoen fra brugernes Enhedsindstillinger, så en bruger, hvis phone har datoen, der er forkert angivet kunne ses i den forkerte tidsperiode.

- Opbevaring: En bruger betragtes som bevares på et bestemt tidsinterval, hvis han har udført sin første session i dette tidsinterval. Du kan ændre tidsintervallerne hvor bevares brugere (og nye brugere), tælles til timer, dage, uger eller måneder. Bruger opbevaring analyser er bygget oven på dyrene i samme kohorte. En kohorte er angivet for alle de nye brugere, der er registreret i en given periode (det vil sige sæt af brugere udføre deres første session i denne periode). Vi bruger dyrene i samme kohorte af 1 dag, 2-dages, 4 dage, 7-dages eller 1 måned. Hvis du har en kohorte, hver 1-dag, 2-dages, 4 dage, 7-dages eller 1 måned, Azure Mobile aftale beregner sæt af alle brugere, der tilhører kohorte og er stadig aktiv (det vil sige sæt af brugere, der udføres mindst én session i perioden). Denne gruppe af brugere, der kaldes en kohorte version. (Azure Mobile aftale kan du se, hvor mange af dine brugere stadig bruger din app, men kun i platform bestemte store kan fortælle dig, hvor mange af dine brugere har fjernet din app – for eksempel GooglePlay iTunes Windows Store, osv.).
- Sessioner: En anvendelse af programmet af en bruger. Sessioner er dannet ud fra række aktiviteter, der udføres af brugere (en aktivitet er som regel tilknyttet brugen af et skærmbillede af programmet, men dette kan variere, afhængigt af den måde, der er integreret i SDK i programmet på computeren). En bruger kan kun udføre en aktivitet ad gangen: en session starter, så snart brugeren starter sin første aktivitet og stopper, når hun er færdig med at sin sidste aktivitet. Hvis en bruger forbliver mere end et par sekunder uden at udføre en hvilken som helst aktivitet, er sin sekvens af aktiviteter opdelt i to forskellige sessioner.
- Aktiviteter: Navnene på de enkelte skærm i dit program og længden af gang brugerne bruge på hvert skærmbillede. Aktiviteter, der er en brugerdefineret analytisk indstilling, som svarer til de "app oplysninger" mærker, du har konfigureret for din egen app:
- Brugersti: Viser, hvordan brugerne navigere gennem programmets aktiviteter (skærme). Du kan flytte skyderen for at justere lydstyrken for detaljer. Blå noder repræsenterer programmets aktiviteter. Deres størrelse er proportional med tiden brugere, der er brugt i den. Hvid noder repræsenterer session start og stop. Rød noder repræsenterer går ned. Links repræsenterer overgange mellem aktiviteter i dit program (eller mellem aktiviteter og går ned). Klik på en node eller et link til at vise et værktøjstip med flere oplysninger om dine data: tid, der bruges i en bestemt skærm, antallet af overgange, og procentdelen af overgange fra den kilde aktivitet på destination aktiviteten. (En---60%---> B betyder, at brugere, der er oprettet på aktivitet A går til aktivitet B 60% af tiden.) Du kan reorganisere grafen, som du vil gøre det klart. dets position gemmes, hver gang du foretager en ændring. Du kan vise eller skjule går ned for at gøre lysere grafen.
- Hændelser: Bestemte handlinger, der udføres af en bruger i programmet på computeren. Fordelingen af hændelser, der vises som antallet af hændelser af hver bruger hver enkelt session. En hændelse repræsenterer en chat handling, for eksempel et klik på en knap eller modtagelse af en meddelelse. (Betydningen af hændelser, der afhænger af hvordan SDK er integreret i programmet.) En hændelse kan opstå, når en session eller et job eller kan være være enkeltstående.
- Job: Minder om begivenheder, bortset fra de fokusere på længden af handlingen. Job kan for eksempel fortæller dig tekniske oplysninger om, hvor lang tid det tager indhold at indlæse eller et opkald til en webtjeneste. Det kan også vise, hvor lang tid det tager en bruger til at udfylde en formular, oprette en konto eller foretager et køb. Et job repræsenterer en opgaves varighed, for eksempel varigheden af en download-opgave eller klokkeslættet et banner vises på skærmen. (Betydningen af sager afhænger af hvordan SDK er integreret i programmet.) Job er som regel tilknyttet baggrundsopgaver, der er udført uden for omfanget af en session (det vil sige uden en hvilken som helst brugeraktivitet).
- Technicals: Tekniske oplysninger om enhederne for brugere af din app, kan du spore, såsom landestandard, Carrier, netværk, enhed, Firmware, og skærmbilledet størrelsen på brugernes enheder, og versionen af din App og den SDK-version, der bruges i din app.
- Fejl: Oplysninger om tekniske fejl i programmet, der ikke forårsager programmet går ned. En fejl repræsenterer et Chat problem, for eksempel en netværksfejl eller en ugyldig strengmanipulation. (Betydningen af hændelser, der afhænger af hvordan SDK er integreret i programmet.) En fejl kan opstå, når en session eller et job eller kan være være enkeltstående.
- Nedbrud: Oplysninger om fejl, der kan medføre programmet går ned. Nedbrud er en uventet betingelse, hvor programmet holder op med at udføre de forventede funktioner og skal stoppes. Nedbrud er som regel på grund af en fejl i programmet.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Få adgang til opbevaring oversigt
![Analytics3][12]

Oversigt i opbevaring er opdelt i midten til flere kort, hver med oversigten til en bestemt opbevaringsperiode. 2-dages opbevaringsperiode kan ses i eksemplet. De andre kort vise 4 dage og 7-dages opbevaring perioder.

## <a name="understanding-the-retention-overview-cards"></a>Forstå opbevaring oversigt-kort
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Hvert kort består af 3 hoveddele:
1. 1: kohorten og perioden i betragtning
2. 2-4: opbevaring for den aktuelle periode
3. 5: et minidiagram af oversigten

### <a name="here-is-detailed-information-about-each-element"></a>Her kan du se detaljerede oplysninger om hvert element:
1.    Kohorte og periode: Denne overskrift giver typen kohorte. "2-dages periode" betyder her ser vi nærmere på funktionsmåden for brugere over 2 dage, brugere, der er modtaget i en periode på 2 dage, og om de forbinde i de følgende blokke på 2 dage. Ovenstående eksempel finder aktiviteten af brugere mellem 21 og 22nd af November.
2.    Dette giver opbevaring rente over 21 og 22 af November for de brugere, der er modtaget i 19 og 20 af November. Her fik vi 1 aktive bruger mellem 21 og 22nd, over 3, der er nye brugere mellem 19th og 20.
3.    Denne visuelle indikator giver de samme oplysninger som ovenfor repræsenteres grafisk. (Tredjepart af cirklen, er fra antallet, 33%). Farven, der giver yderligere oplysninger: grøn indikerer, at dette nummer vokser fra den forrige beregning. Gul betyder stabil og røde betyder, at reducere.
4.    Dette angiver de værdier, der bruges til beregning.
5.    Dette er et minidiagram af oversigten over opbevaring værdierne. Gør det muligt at få vist værdierne i de seneste skal have en bred visning af hvordan det udviklet.


## <a name="see-also"></a>Se også

- [Begreber][Link 6]
- [Fejlfinding i forbindelse med Guide Service][Link 24]

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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
