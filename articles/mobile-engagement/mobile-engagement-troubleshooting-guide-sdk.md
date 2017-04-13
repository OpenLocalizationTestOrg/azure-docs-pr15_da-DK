<properties 
   pageTitle="Azure Mobile aftale fejlfindingsvejledningen - SDK" 
   description="Fejlfinding af problemer med SDK-integration i Azure Mobile aftale" 
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

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Fejlfinding i forbindelse med vejledning til SDK integrationsproblemer

Følgende er mulige problemer, der kan opstå med hvordan Azure Mobile aftale integrerer i dit program.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>SDK problemer, der er fundet ved en fejl i et andet område i dit program

### <a name="issue"></a>Problem
- Brugergrænsefladen data samling fejl (under analyse, overvågning, segmentering eller Dashboards).
- Push-fejl (skubber virker ikke i app af app eller begge).
- Avanceret funktion fejl (registrering, Geolokalitet eller platform bestemte skubber ikke virker).
- API fejl (API'er fejl ofte uovervåget uden fejlmeddelelser).
- Fejl på tjeneste (intet af Azure Mobile aftale hjælper for dit program).

### <a name="causes"></a>Årsager

- De fleste problemer, der skal løses med Azure Mobile aftale SDK bliver fundet ved en fejl i dit program (som en brugergrænseflade data samling fejl, opslagsnål fejl, avanceret funktion mislykkedes, API mislykkedes, programmer går ned eller synlige service afbrydelse).  
- Hvis en bestemt funktion i Azure Mobile aftale har aldrig arbejdet i din app før, skal du udføre integration. 
- Hvis en bestemt funktion i Azure Mobile aftale blev arbejde og ikke længere, kan du har brug at opgradere til den sidste version med Azure Mobile aftale SDK. Husk, at der er en anden version af Azure Mobile aftale SDK for hver platform, der understøttes af Azure Mobile aftale (Android, iOS, Windows og Windows Phone).

#### <a name="sdk-integration"></a>Integration af SDK

- Azure Mobile aftale integreret ikke korrekt i SDK (Analytics).
- Når integreret ikke korrekt i SDK (i App og ikke til App skubber).
- Certifikatet udløbet eller forkert Prod.ordre kontra Udviklingscenter (kun iOS).
- GCM eller ADM ikke korrekt integreret i SDK (Android kun - tjenesten bestemte-skubber).
- Integreret sporing ikke korrekt i SDK (Installer store sporing).
- Fløde placering eller GPS placering integreret ikke korrekt i SDK (Targeting efter geografisk placering).


**Se også:**

- [Dokumentationen SDK - Integration hjælpelinjer][Link 5] 
- [Fejlfindingsvejledningen - opslagsnål][Link 23]

#### <a name="sdk-upgrade"></a>SDK opgradering

- Har du brug for at opgradere SDK for at løse problemer med ældre versioner af SDK (ofte relateret til nyere versioner af enheden OS).
- Fjerne alle tidligere versioner af din app fra enheden og geninstallere den nyeste version af din app, igen registrere din enheds-ID fra Azure Mobile aftale Brugergrænsefladen til at bekræfte, at enheden bruger den nyeste version af din app.

**Se også:**

- [Dokumentationen SDK - produktbemærkninger](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Dokumentationen SDK - opgradering hjælpelinjer](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK andre

- Fejl i programmanifestet "AndroidManifest.xml" kan medføre Azure Mobile aftale ikke til at fungere sammen (kun Android).
- Der er et almindelige problemer med SDK integration og brug af API til at forveksle nøglen SDK og API-nøgle.

**Se også:**

- [Begreber - ordliste][Link 6]

## <a name="advanced-coding-issues"></a>Avancerede kodningssprog problemer

### <a name="issue"></a>Problem
-  Platform bestemt kode, der ikke er direkte relateret til Azure Mobile aftale kan medføre problemer på iOS-, Android- og Windows Phone.

### <a name="causes"></a>Årsager

- Mange avancerede koder problemer med Azure Mobile aftale skyldes forkert skrevet platform bestemt kode, der ikke er direkte relateret til Azure Mobile aftale. Du skal se dokumentationen, der er specifikke for platformen du udvikler ud over Azure Mobile aftale dokumentation (Android, iOS, Web, Windows og Windows Phone).
- Ikke korrekt konfiguration af "kategorier", forhindrer, at oprette en kæde fra en meddelelse til en anden placering i eller uden for app (kun Android). 
- Ikke indstille "UIKit.framework" til "valgfri" i din iOS-kode, viser en "Symbol blev ikke fundet fejl" og/eller går ned på ældre iOS-enheder (kun iOS).
- Udløbne certifikater eller ikke korrekt ved hjælp af den Udviklingscenter eller Prod.ordre version af cert, årsager opslagsnål problemer (kun iOS).
- Der er visse begrænsninger, der er forbundet til en platform, der ikke kan styres Azure Mobile aftale (såsom hvordan system center fungerer til af app'en skubber i Android- og iOS).
- Azure Mobile aftale publicerer en komplet liste over de interne pakker, der bruges af Azure Mobile aftale til iOS og Android til reference. Husk på, at nogle funktioner i Azure Mobile aftale er specifikke for platform (Android, iOS, Web, Windows og Windows Phone).

### <a name="see-also"></a>Se også

 - [Fejlfindingsvejledningen - opslagsnål][Link 23] 
 - [Dokumentationen SDK - produktbemærkninger][Link 5]
 - [Dokumentationen SDK - opgradering hjælpelinjer][Link 5]

## <a name="application-crashes"></a>Programmet går ned

### <a name="issue"></a>Problem
- Programmet går ned på den slutbrugere enhed.

### <a name="causes"></a>Årsager

- Nedbrud oplysninger kan ses i *Analytics brugergrænseflade* eller *Analytics API*
- Du kan finde enheden test enheds-ID og tage den samme handling, der forårsagede dit program til at gå ned til slutbrugeren for at identificere årsagen til din gå ned.
- Kendte problemer med Azure Mobile aftale SDK, der forårsager programmer går ned, der nogle gange kan løses ved at opgradere til den nyeste version af SDK. Sørg for at kontrollere produktbemærkninger om din platform, når ved at undersøge går ned.

### <a name="see-also"></a>Se også

- [Dokumentationen SDK - produktbemærkninger][Link 5]
- [Dokumentationen SDK - opgradering hjælpelinjer][Link 5]

## <a name="app-store-upload-failures"></a>App store Overfør fejl

### <a name="issue"></a>Problem
- Fejl, der er relateret til overførsel af den seneste version af din app til Apple, Google eller Windows App store.

### <a name="causes"></a>Årsager

- App gemmer nogle gange Bloker apps med bestemte funktioner, der aktiveres (fx Apple Store forhindrer brugen af IDFV i Office store og GooglePlay store forhindrer Deling af programoplysninger mellem apps). 
- Sørg for, at du kontrollerer produktbemærkninger om din platform og aktuelle version af SDK, hvis du har problemer med overførsel af en app til store.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
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
 
