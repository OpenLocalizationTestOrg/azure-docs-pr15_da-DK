<properties 
   pageTitle="Azure Mobile aftale brugergrænseflade - min konto" 
   description="Lær, hvordan du administrerer din konto profil og test enheder med Azure Mobile aftale" 
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

# <a name="how-to-manage-your-account-profile-and-test-devices"></a>Sådan administreres enheder til profil og test din konto
 
I denne artikel beskrives siden **Hjem** i portalen **Mobile aftale** . Du kan bruge portalen **Mobile aftale** til at overvåge og administrere dine mobile-apps. 
 
Klik på kontoen øverst på siden for at komme til siden **Min konto** .

Afsnittet min konto i Brugergrænsefladen er, hvor du kan få vist og ændre de indstillinger, der er knyttet til din konto, herunder profilindstillingerne og teste enhed id'er. Disse indstillinger indeholder elementer, der kan også få adgang til via API'EN enhed.

![MyAccount1][7]  

## <a name="profile"></a>Profil:
Du kan få vist eller ændre nogen af indstillingerne for din konto, som er vist nedenfor. Du kan også give en anden brugertilladelse til at bruge dit program, der er baseret på deres mailadresse fra [Home](mobile-engagement-user-interface-home.md).

![MyAccount2][8]  

## <a name="devices"></a>Enheder:
Du kan se, tilføje eller fjerne teste enhed id'er af de test-enheder, du kan bruge til at teste din **Når** eller **push** kampagner. Kontekstafhængig instruktioner til at finde enheder for hver platform enheds-ID (iOS, Android, Windows Phone, osv.) vises, når du klikker på "Nye enhed". 
 
![MyAccount3][9]  
 
Du skal kende dine brugeres entydigt enheds-id (parameteren deviceid) for at bruge Push-API eller enhed API. Der er flere måder at hente det:
 
1. Du kan bruge funktionen "Hent" i enhed API til at få den komplette liste over enhed id'er fra din back-end.
2. Du kan bruge SDK fra din app skal du have den. (Kalde på Android-enhed, funktionen getDeviceID() på klassen, Agent, og på iOS, læse egenskaben deviceid for klassen Agent.)
3. Fra en rækkevidde meddelelse, hvis den handling URL-adresse, der er knyttet til meddelelsen, der indeholder et mønster {deviceid} erstattes den automatisk af id'et for den enhed, der udløser handlingen.
http://<example>.com/registeruser?deviceid = {deviceid} & otherparam = myparamdata erstattes af: http://<example>.com/registeruser?deviceid = XXXXXXXXXXXXXXXX & otherparam = myparamdata 
4. Fra en rækkevidde web meddelelse, hvis HTML-koden for meddelelsen, der indeholder et mønster {deviceid} erstattes den automatisk af id'et for den enhed, der viser web meddelelsen.
Her er min enheds-id: {deviceid} erstattes af: Her kan du se min enheds-id: XXXXXXXXXXXXXXXX
5.  Åbne dit program på enheden og udføre en begivenhed i din app, som er blevet mærket.
Finde den begivenhed, du har udført i listen fra "Brugergrænseflade - din app - skærm - begivenheder - oplysninger".
Klik på til begivenheden i skærmen.
Du skal finde din enheds-ID på listen over de enheder, der er udført begivenheden.
Derefter kan du kopiere denne enheds-ID og registrere den "Brugergrænseflade - min konto - enheder - nye enhed - Vælg din enhed platform".
>(Være opmærksom på, når IDFA er deaktiveret til iOS, enheds-ID måske ændres med tiden Hvis du fjerne og geninstallere din app).

##<a name="troubleshooting-guide"></a>Vejledning til fejlfinding
-  [Fejlfindingsvejledningen - tjenesten][Link 24]

## <a name="see-also"></a>Se også
-  [Brugergrænsefladen dokumentation - startside][Link 13]


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


 
 
