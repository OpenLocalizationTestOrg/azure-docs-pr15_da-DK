<properties 
   pageTitle="Azure Mobile aftale brugergrænseflade - rækkevidde kriterium" 
   description="Lær at bruge målretning kriterier til at sende opslagsnål kampagner til et Vælg undersæt af dine brugere ved hjælp af Azure Mobile aftale" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Sådan bruges kriterierne for målretning til at sende opslagsnål kampagner til et Vælg undersæt af dine brugere

Målretning af målgruppen efter bestemte kriterier med knappen "Ny kriterier" er et af de mest effektive begreber i Azure Mobile aftale, hjælper med at du sender relevante push-beskeder, kunderne reagerer på i stedet for Lad alle. Du kan begrænse målgruppen er baseret på standard kriterier og simulere skubber til at afgøre, hvor mange personer, modtager meddelelsen.

**Se også:**

- [Brugergrænsefladen dokumentation - rækkevidde - nye opslagsnål kampagnens][Link 27]

## <a name="audience-criteria-can-include"></a>Målgruppe kriterier kan omfatte:
- **Technicals:** Du kan målrette baseret på de samme tekniske oplysninger, du kan se i sektionerne analyser og skærm. **Se også:** [Brugergrænsefladen dokumentation - analyser] [ Link 15], [Brugergrænsefladen dokumentation - skærm][Link 16]
- **Placering:** Programmer, der bruger "realtid placering rapportering" med geografisk indhegning kan bruge geografisk placering som et kriterium målrette en målgruppe fra GPS-placering. "Fløde området placering rapportering" opkald også bruges til at målrette en målgruppe fra mobiltelefon placering ("realtid placering rapportering" og "Fløde området placering rapportering" skal være aktiveret fra SDK). **Se også:** [SDK dokumentation - iOS - Integration] [ Link 5], [SDK dokumentation - Android - Integration][Link 5]
- **Når Feedback:** Du kan målrette dit publikum, der er baseret på deres feedback fra forrige rækkevidde beskeder via rækkevidde feedback fra meddelelser, forespørgsler og flytter Data. Dette gør det muligt at bedre target målgruppen efter to eller tre når kampagner, end du kunne første gang. Det kan også bruges til at filtrere brugere, der allerede har modtaget en meddelelse med lignende indhold ved at angive en kampagnens ikke sendes til brugere, som allerede har modtaget en bestemt forrige campaign. Du kan endda udelukke brugere der medtages en bestemt kampagnens, der stadig er aktiv i at modtage nye skubber. **Se også:** [Brugergrænsefladen dokumentation - når - Push-indhold][Link 29]
- **Installere registrering:** Du kan spore oplysninger baseret på, hvor brugerne installeret din App. **Se også:** [Brugergrænsefladen dokumentation - indstillinger][Link 20]
- **Brugerprofil:** Du kan målrette efter standard brugeroplysninger, og du kan target baseret på den brugerdefinerede app-oplysninger, du har oprettet. Dette omfatter brugere, der er logget på og brugere, der har besvaret specifikke spørgsmål, du har bedt om at angive i selve appen i stedet for blot hvordan de har reageret på forrige kampagner. Alle dine App oplysninger defineret for din app vises på denne liste.
- Segmenter: Du kan også target baseret på målgrupper, du har oprettet på baggrund af bestemt brugeradfærd, der indeholder flere kriterier. Alle dine målgrupper, der er defineret for din app vises på denne liste. **Se også:** [Brugergrænsefladen dokumentation - målgrupper][Link 18]
- **App oplysninger:** Brugerdefinerede App oplysninger koder kan oprettes "Indstillinger" til at registrere bruger funktionsmåde. **Se også:** [Brugergrænsefladen dokumentation - indstillinger][Link 20]

## <a name="example"></a>Eksempel: 
Hvis du vil skubbe en meddelelse kun til de underordnede dine brugere, der har udført en handling til køb af i-app.

1. Gå til indstillingssiden for dit program skal du vælge menuen "App oplysninger" og vælge "Ny app oplysninger"
2. Registrere en ny boolesk app-oplysninger, der kaldes "inAppPurchase"
3. Gøre programmet angive denne app oplysninger til "true", når brugeren udfører korrekt Køb en i-app (ved hjælp af sendAppInfo ("inAppPurchase",...) funktionen)
4. Hvis du ikke vil gøre dette fra dit program, kan du gøre det fra din back-end ved hjælp af enheden API)
5. Derefter skal du blot vil oprette din meddelelse med et kriterium, at begrænse målgruppen til brugere, der har "inAppPurchase", som er angivet til "true")
 
> Bemærk: Målretning af baseret på flere kriterier end app oplysninger mærker kræver Azure Mobile aftale til at indsamle oplysninger fra dine brugeres enheder, før opslagsnål sendes, og så kan medføre en forsinkelse. Kompleks opslagsnål konfiguration indstillinger (som opdatering badges) kan også forsinke flytter. Brug af en "ét skærmbillede" kampagnens fra opslagsnål API er absolut hurtigste push-metoden i Azure Mobile aftale. Brug af kun app oplysninger mærker som push kriterier for en rækkevidde kampagnens (enten i API når eller Brugergrænsefladen) er den næste hurtigste metode, da app oplysninger mærker er gemt på serversiden. Brug af andre målretning kriterier for en opslagsnål kampagnens er mest fleksible men lavest push-metoden, da Azure Mobile aftale har at forespørge på enhederne for at kunne sende kampagnens.
 
![Rækkevidde Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Indstillinger for kriterium gælder for:
- **Technicals**     
- Firmware navn: Firmware navn
- Firmware-version: Firmware-version
- Enhedsmodel: enhedsmodel
- Producenten af enheden: producenten af enheden
- Programmet version: programmet version
- Carrier navn: Carrier navn, ikke defineret
- Carrier land: Carrier land, Udefineret
- Netværk type: netværk type
- Landestandard: landestandard
- Skærmstørrelse: skærmstørrelse
- **Placering**      
- Sidst kendte område: land, Region lokalitet
- Realtid geografisk-indhegning: listen over interessepunkter (navn og handlinger), cirkulær p (navn, vestkyst, længdegrader, Radius i meter)
- **Når feedback**     
- Om Familieforøgelse feedback: meddelelse, feedback
- Afstemning feedback: afstemning, feedback
- Afstemning answer feedback: afstemning answer feedback, spørgsmål, valg
- Data opslagsnål feedback: Data opslagsnål, feedback
- **Installere registrering**     
- Store: Gemme, ikke defineret
- Kilde: Kilde Udefineret
- **Brugerprofil**     
- Køn: han eller hun, Udefineret
- KID dato: operator, dato, Udefineret
- Tilmelding: SAND eller FALSK, ikke defineret
- **App oplysninger**      
- Streng: Streng, der ikke defineret
- Dato: operator, dato, Udefineret
- Heltal: operator, tal, ikke defineret
- Boolesk: true eller false, Udefineret
- **Målgruppe**    
- Navnet på segmenter (fra rullelisten) udelukkelse (target brugere, der ikke er en del af dette segment).

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
 
