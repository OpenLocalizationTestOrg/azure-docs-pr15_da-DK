<properties 
   pageTitle="Azure Mobile aftale brugergrænseflade - målgrupper" 
   description="Lær at oprette og administrere målgrupper for brugerne at identificere mønstre i brugen af ved hjælp af Azure Mobile aftale" 
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

# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Hvordan du kan oprette og administrere målgrupper for brugerne at identificere mønstre i brugen af

I denne artikel beskrives fanen **SEGMENTER** i portalen **Mobile aftale** . Du kan bruge portalen **Mobile aftale** til at overvåge og administrere dine mobile-apps.

Afsnittet segmenter i Brugergrænsefladen kan du arbejde på segmentering dine brugere, der er baseret på forskellig adfærd og analyse, som du kan få fra programmet på computeren og kan også få adgang via segmenter API. Segmenter er først beregnes 24 timer, når de er oprettet, og de genberegnes døgnet baseret på de seneste analytics-oplysninger. Når en målgruppe beregnes, vises et "Dag til dag oversigt" diagram hver dag.


>[AZURE.NOTE] Mange sektioner af portalen **Mobile aftale** Brugergrænsefladen indeholder knappen **Vis Hjælp** . Tryk på denne knap for at få flere kontekstafhængige oplysninger om en sektion.

## <a name="create-segments"></a>Oprette målgrupper
Du kan oprette en målgruppe baseret på op til 10 kriterier på en bestemt periode op til 60 dage tidligere i afsnittet analyser. For eksempel kan du oprette en målgruppe baseret på de personer, der har fået vist bestemte sider eller søger efter specifikt indhold i din app i de seneste 10 dage. Disse oplysninger er tilgængelige i sektionen analyser. Tilfældet, kan du bruge det til at oprette en målgruppe, og derefter konfigurere en opslagsnål meddelelse målrette denne undersæt af brugere til at få dem til at komme tilbage til programmet. 
 
> Bemærk: Når en målgruppe er beregnet, den kan ikke redigeres; Det kan kun klonet (kopieret) eller ødelagt (slettet). En målgruppe kan klones i det samme program (med det samme sikkerhedskontrol), og det kan også klones til andre programmer (med en anden sikkerhedskontrol). 
 
 ![segments1][35] 

## <a name="examples-segments"></a>Eksempler målgrupper
 ![segments2][36]

Segmenter gør det muligt at inddele slutbrugere af dit program.
Segmentering brugerne er en vigtig marketing strategi for. Azure Mobile aftale giver dig mulighed at hente historiske data og oprette brugerdefinerede målgrupper. Denne effektivt værktøj gør det muligt at få mere at vide om kundernes oplevelse i programmet. Du kan nemt analysere dine målgrupper og bruge målgrupper som push destinationer.
En fælles use case er, at du vil sende en opslagsnål en meddelelse om at fremme dine slutbrugere kan bedømme dit program i store. I stedet for at sende en meddelelse til alle dine slutbrugere, kan du oprette en målgruppe, der ville angive kun brugere, der har brugt dit program hver dag til den sidste måned og har haft en god oplevelse. Når du sender færre, meget målrettede pushmeddelelser, får du en bedre afkast.
 
 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Målgrupper, du kan oprette baseret på de vigtigste Azure Mobile aftale elementer:
- Begivenhed: oprette en målgruppe, destinationer en bestemt hændelse på det program, er der sket med mere end to gange om ugen. 
- Session: oprette en målgruppe af brugere, der har brugt programmet mere end 5 gange sidste uge.
- Aktivitet: oprette en målgruppe af brugere, der har brugt en side eller indhold mere eller mindre end 10 tid sidste måned.
- Kørsel: oprette en målgruppe af brugere, der har fuldført et job mere end to gange om dagen.
- Fejl: oprette en målgruppe for alle de brugere, der har haft nedbrud mere end 10 gange sidste uge. (Du kan skubbe dette segment med en undskyldning eller endda en kupon!)
- Fejl: oprette en målgruppe for alle de brugere, der har haft en fejl mere end 100 gange sidste 3 dage.
- App-oplysninger: oprette en målgruppe, der er beregnet til en brugerdefineret App-oplysninger, der er der sket med de seneste 25 dage.
 
 ![segments4][38]

Du har lavet en tilpasset integration af SDK i dit program med en mærkning plan over "App oplysninger" mærker for at bruge segmentet optimalt skal.
Gå derefter til startsiden for grænsefladen skal du vælge det ønskede program, og klik på sektionen "Segmenter".

1. Vælge afsnittet "Segmenter".
2. Klik på "ny målgruppe" knappen for at oprette en ny målgruppe.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Virkelige liv eksempel: Oprette en simpel målgruppe baseret på oplysninger om "Sessionen"
Oprette en målgruppe af alle slutbrugere, der har brugt din app mindst 50 gange i den sidste uge. Find kun de slutbrugere, der har brugt mindst 30 sekunder i din app hver enkelt session derfra. Dette viser alle slutbrugere, der har en god oplevelse i din app. Segmentet oprettet kan derefter bruges til at overføre en meddelelse til disse slutbrugere kan bede dem om at bedømme din app store.
 
 ![segments5][39]

1. Giv din målgruppe et navn for at finde det hurtigt på listen "Segmentet".
2. Klik på knappen "Opret".
 
 ![segments6][40]

Vælg Session.
 
 ![segments7][41]

1. Vælg perioden "Sidste uge".
2. Klik på Næste.
 
 ![segments8][42]

1. Vælge den Operator, der er relevant mellem listen: =; ≥, ≤.
2. Angiv det antal, du vil.
3. Vælg den ønskede forekomst. 
4. Klik på Næste.
I dette eksempel er angivet, så, over den sidste uge, svarer til brugere, der har foretaget mindst 50 sessioner.
 
 ![segments9][43]

Du kan vælge længden hver enkelt session som et kriterium for segmenteringen Session.

1. Vælge operatoren, på listen.
2. Angiv længde hver enkelt session.
3. Klik på Næste.
I dette eksempel står der, at over alle sessioner, der har været adskilt i afsnittet forekomst skal du vælge kun de brugere, der har brugt mere end 30 sekunder hver enkelt session.
 
 ![segments10][44]

Navngiv et kriterium for at hente det i den komplette salgskanal, og klik på Udfør.
 
 ![segments11][45]

Når du har konfigureret et kriterium, vises den i segmentet salgskanal.
Da en målgruppe baseret på analytics-data, er segmenter beregnes en gang om dagen.
I dette eksempel 47,7% af de samlede slutbrugere matchet kriteriet. De skal være de brugere, der har haft en god oplevelse og bliver sandsynligvis giver en højere bedømmelse, hvis du push dem en meddelelse, hvor du beder dem til at vurdere app store.


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
 
