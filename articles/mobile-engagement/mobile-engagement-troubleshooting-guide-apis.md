<properties 
   pageTitle="Azure Mobile aftale fejlfindingsvejledningen - API'er" 
   description="Fejlfinding i forbindelse med vejledninger til Azure Mobile aftale - API'er" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/04/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-api-issues"></a>Fejlfinding i forbindelse med vejledning til API problemer

Følgende er mulige problemer, der kan opstå med hvordan administratorer interagere med Azure Mobile aftale via API'erne.

## <a name="syntax-issues"></a>Syntaksen for problemer

### <a name="issue"></a>Problem
- Syntaksfejl ved hjælp af API (eller uventet funktionsmåde).

### <a name="causes"></a>Årsager

- Syntaksen for problemer:
    - Sørg for at kontrollere syntaksen i bestemte API du bruger til at bekræfte, at indstillingen er tilgængelig.
    - Der er et almindelige problem med brug af API til at forveksle når API og Push-API (de fleste opgaver skal udføres med når API i stedet for Push-API). 
    - Der er en anden almindelige problemer med SDK integration og brug af API til at forveksle tasten SDK og API-nøgle.
    - Scripts, der har forbindelse til API'erne skal bruge til at sende data mindst én gang 10 minutter, eller forbindelsen afbrydes (især fælles i skærm API scripts lytte til data). Hvis du vil forhindre timeout, har scriptet til at sende en XMPP ping hver 10 minutter at holde sessionen Live med serveren.

### <a name="see-also"></a>Se også
 
- [API dokumentation][Link 4]
- [XMPP Protocol oplysninger]( http://xmpp.org/extensions/xep-0199.html)
 
## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>Ikke bruge API til at udføre den samme handling, der er tilgængelige i brugergrænsefladen i Azure Mobile aftale.

### <a name="issue"></a>Problem
- En handling, som egner sig fra Azure Mobile aftale Brugergrænsefladen fungerer ikke fra relaterede Azure Mobile aftale API.

### <a name="causes"></a>Årsager

- Bekræfte, at du kan udføre den samme handling fra Azure Mobile aftale Brugergrænsefladen viser, at du har integreret denne funktion i Azure Mobile aftale korrekt med SDK.

### <a name="see-also"></a>Se også
 
- [Brugergrænsefladen dokumentation][Link 1]
 
## <a name="error-messages"></a>Fejlmeddelelser

### <a name="issue"></a>Problem
- Fejlkoder ved hjælp af API vises på kørselstidspunktet eller i logfiler.

### <a name="causes"></a>Årsager

- Her er et sammensat liste over almindelige API status koder tal til reference og foreløbige fejlfinding:

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Se også

- [API dokumentation - for detaljerede fejl på hver bestemt API][Link 4]
 
## <a name="silent-failures"></a>Uovervåget fejl

### <a name="issue"></a>Problem
- API handling mislykkes uden fejlmeddelelse, der vises på kørselstidspunktet eller i logfiler.

### <a name="causes"></a>Årsager

- Mange elementer vil deaktiveres Azure Mobile aftale brugergrænsefladen, hvis de ikke er integreret korrekt, men mislykkes uovervåget fra API, så Husk at teste de samme funktioner fra Brugergrænsefladen til at se, hvis det fungerer.
- Azure Mobile aftale, og mange avancerede funktioner i Azure Mobile aftale, du forsøger at bruge, skal være individuelt integreret i din app med SDK som separate trin før du kan bruge dem.

### <a name="see-also"></a>Se også

- [Fejlfindingsvejledningen - SDK][Link 25]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
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
 
