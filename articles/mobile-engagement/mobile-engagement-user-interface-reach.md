<properties 
   pageTitle="Azure Mobile aftale brugergrænseflade - rækkevidde" 
   description="Lær, hvordan du Henvend dig til brugerne af dit program med pushmeddelelser ved hjælp af Azure Mobile aftale" 
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


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Sådan Henvend dig til brugerne af dit program med pushmeddelelser

I denne artikel beskrives fanen **Når** i portalen **Mobile aftale** . Du kan bruge portalen **Mobile aftale** til at overvåge og administrere dine mobile-apps. Bemærk, at for at starte ved hjælp af portalen skal du først oprette en **Aftale til Azure Mobile** -konto. Få mere at vide under [oprette en Azure Mobile aftale konto](mobile-engagement-create.md).

Afsnittet rækkevidde i Brugergrænsefladen er administrationsværktøjer opslagsnål kampagnens, hvor du kan oprette/Rediger/aktivere/Afslut/skærm og få statistik på opslagsnål meddelelse kampagner og funktioner, der kan også få adgang til via når API (og visse elementer af niveauet lav Push-API). Husk, om du bruger API'erne eller Brugergrænsefladen, skal du integrere både Azure Mobile aftale og rækkevidde i dit program for hver platform med SDK før du kan bruge når kampagner.

>[AZURE.NOTE] Mange sektioner af portalen **Mobile aftale** Brugergrænsefladen indeholder knappen **Vis Hjælp** . Tryk på denne knap for at få flere kontekstafhængige oplysninger om en sektion.

## <a name="four-types-of-push-notifications"></a>Fire typer pushmeddelelser
1.    Meddelelser - giver dig mulighed for at sende reklamer meddelelser til brugere, dirigeres til en anden placering i din app eller til at sende dem til en webside eller store uden for din app. 
2.    Afstemninger - gør det muligt at indsamle oplysninger fra slutbrugere ved at stille dem spørgsmål.
3.    Data skubber - gør det muligt at sende et binært tal eller base64 datafil. Oplysningerne i en data opslagsnål sendes til dit program til at ændre dine brugeres aktuelle oplevelse i din app. Dit program skal have lov til at behandle dataene i et enkelt tryk data.

## <a name="campaign-details"></a>Detaljer om kampagner

Du kan redigere, klone, slette eller aktivere kampagner, der ikke er blevet aktiveret, ved at holde musen over deres navne, eller du kan klikke på for at åbne dem. Du kan klone kampagner, der allerede er aktiveret ved at holde musen over deres navne, eller du kan klikke på for at åbne dem. Du kan ikke ændre en kampagnens, når den er blevet aktiveret.
 
![Reach1][18]

## <a name="reach-feedback"></a>Når Feedback

Klik på **Statistik** for at se detaljerne for en rækkevidde campaign. Visningen **simpel** giver en visuel præsentation i form af et liggende søjlediagram om hvad der er sket, når en kampagnens er aktiveret. Visningen **Avanceret** finde mere detaljerede oplysninger om opslagsnål campaign. Disse oplysninger er ikke tilgængelige, hvis du sender en test kampagnens det vil sige en opslagsnål, der er sendt til en test-enhed. Her er, hvordan du skal fortolker disse oplysninger:

1. **Pushed** - angiver antallet af meddelelser, der overføres til enhederne. Dette nummer, afhænger af målgruppe, du har angivet, mens du opretter opslagsnål campaign. Hvis du ikke angiver en målgruppe, derefter sendes denne opslagsnål til alle de registrerede enheder. Som alle andre push-tjenester vi ikke pushbeskeder de direkte til enhederne, men i stedet overføre dem til de respektive platform specifikke opslagsnål meddelelse tjenester (PNS - GCM-APNS/WNS) så de kan levere meddelelserne til enhederne. 

2.  **Leveret** - angiver antallet af meddelelser, som er blevet leveret af PNS på enheden og bekræftet som modtagne ved Mobile aftale SDK. 
        
    *Årsager til leveret tælle er mindre end udskudt antal:*
    
    1. Hvis brugeren har fjernet appen fra enheden, men PNS ikke ved, om det på det tidspunkt, vi sender tryk til PNS fjernes meddelelsen.
    2. Hvis enheden har programmet, men enhederne, selv var offline i lang tid, mislykkes PNS at levere meddelelsen til enheden. 
    3. Hvis meddelelsen få leveret til enheden, men Mobile aftale SDK i app ikke genkender indholdet af meddelelsen, udelader meddelelsen. Dette kan ske, hvis tilpasning af meddelelsen til i appen genererer en undtagelse som vi fange i SDK og slip meddelelsen. Dette kan også ske, hvis app på enheden bruger en version af Mobile aftale SDK, ikke der kunne forstå den nyere version af push-meddelelse fra platformen, men det er kun, når appen blev opgraderet, når meddelelsen blev sendt fra tjenesten platformen. Under fanen **Avanceret** fortæller, hvor mange meddelelser er gået tabt. 
    4. På iOS-enheder, meddelelser, nogle gange ikke få leveret Hvis enten enheden er på lavt batteriniveau, eller hvis appen er forbrug betydeligt mængde strøm, når behandling af eksterne meddelelser. Dette er en begrænsning af iOS-enheder.   

3.  **Viste** - angiver antallet af meddelelser, som er blevet indsat til app-bruger på enheden i form af en meddelelse om system push/fra-i-app i midten meddelelse eller i app'en vises i mobilappen.  Under fanen **Avanceret** fortæller dig, hvor mange blev systemmeddelelser og hvor mange var i app-meddelelser. 
    
    *Årsager til viste tælle er mindre end leveret antal (venter på at blive vist)*
    
    1. Hvis meddelelse kampagnens havde en dato på den er det muligt, at meddelelsen blev leveret, men når tid, der blev leveret til at åbne og få den vist til app-bruger, blev det allerede udløbet så den er aldrig vises.   
    2. Hvis meddelelsen er en appmeddelelse, og klik derefter på beskeden vises kun, når brugeren app åbner appen. I tilfælde, hvor app brugeren ikke har åbnet app, rapporterer SDK, at meddelelsen blev leveret, men endnu ikke er vist før appen er åbnet. 
    2. Hvis meddelelsen er en appmeddelelse og konfigureret skal vises på en bestemt aktivitet/skærm, og derefter også meddelelsen rapporteres som leveret, men endnu ikke er leveret indtil åbner brugeren app på en bestemt skærm. 
    
4.  **Bruger interaktioner** – dette angiver antallet af beskeder som app brugeren har været i forbindelse med og indeholder de meddelelser, der er actioned eller afsluttet. 

    - *App brugeren kan handling en besked på en af følgende måder:*
            
        1. Hvis meddelelsen er en meddelelse om system/fra-i-app eller en i-app-meddelelse, der sendes som meddelelse kun derefter app brugeren klikker på meddelelsen.
        2. Hvis meddelelsen er en meddelelse om i app'en med en tekst eller web-visning eller afstemninger derefter app brugeren klikker på knappen handling i meddelelsen.
        3. Hvis meddelelsen er en meddelelse om i app'en med en webvisning derefter klikker app brugeren på en URL-adresse i webvisning [kun Android]
    
    - *App brugeren kan lukke en meddelelse i en af følgende måder:*
    
        1. Klik på knappen Luk meddelelsen direkte. 
        2. At stryge ikke til stede, eller slette meddelelsen. 
        3. I app-meddelelser med tekst/webindhold og afstemninger vises typisk til app-bruger i en proces. De først få vist en meddelelse, og når man klikker på den, kan brugeren se efterfølgende tekst/web/afstemning indholdet. App brugeren kan afslutte en meddelelse i et af disse trin, og oplysningerne i visningen Avanceret registrerer dette. 

5.  **Actioned** - angiver antallet af meddelelser, der var eksplicit actioned af app brugeren. Dette er den mest interessante tal, som dette fortæller, hvor mange app brugere er interesseret ved den meddelelse, du har trykket på meddelelsen. 
 
> [AZURE.NOTE] Platforme, hvis brugeren har programmet åbne på iOS & Windows og for kampagnens kampagnens "Når som helst" derefter det er muligt, at begge af app og i app-meddelelser vises på samme tid. Dette kan medføre en viste tælling, der er højere end leveret. Hvis brugeren skal arbejde sammen eller handlinger meddelelsen, og klik derefter selv bruger interaktioner/Actioned antallet kan være større end leveret. 


![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
