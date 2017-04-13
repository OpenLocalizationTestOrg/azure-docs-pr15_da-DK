<properties 
    pageTitle="Azure meddelelse Hubs - diagnosticering retningslinjer" 
    description="Vejledning i at diagnosticere almindelige problemer med Azure meddelelse Hubs." 
    services="notification-hubs" 
    documentationCenter="Mobile" 
    authors="ysxu" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="NA" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs---diagnosis-guidelines"></a>Azure meddelelse Hubs - diagnosticering retningslinjer

##<a name="overview"></a>Oversigt

En af de mest almindelige spørgsmål, som vi høre fra Azure meddelelse Hubs kunder er hvordan til at finde ud af, hvorfor de ikke kan se en meddelelse, der sendes fra deres programmet back-end vises på klientenhed - hvor og hvorfor meddelelser er gået tabt, og hvordan du kan løse dette problem. I denne artikel vil vi gå gennem de forskellige årsager hvorfor meddelelser muligvis får opgivet eller ikke slutter i enhederne. Vi ser også gennem måder, som du kan analysere og finde ud af den egentlige årsag. 

Først, det er vigtigt at forstå, hvordan Azure meddelelse Hubs flytter notifikationer til enhederne.
![][0]

I et typisk send besked om flow sendes meddelelsen fra **programmet backend-version** til **Azure meddelelse Hub (NH)** , som også indeholder nogle behandling på alle registreringer, der tages højde for konfigurerede mærker og mærke udtryk til at bestemme "mål" det vil sige alle registreringer, der skal modtage opslagsnål meddelelsen. Disse registreringer kan dække på tværs af et eller alle af vores understøttede platforme - iOS, Google, Windows, Windows Phone Kindle og Baidu for Kina Android. Når der er oprettet målene, NH og derefter skubber notifikationer, opdele på tværs af flere mængde registreringer til enhed platformen bestemte **Push meddelelse Service (PNS)** - fx APNS til Apple, GCM til Google osv. NH godkender med de respektive PNS ud fra de legitimationsoplysninger, der konfigureres i portalen Azure klassisk på siden Konfigurer besked om Hub. PNS derefter videresender meddelelser til de respektive **klientenheder**. Dette er den anbefalede måde at levere pushmeddelelser og Bemærk, at den sidste del af levering af meddelelse finder sted mellem platform PNS og enheden platform. Derfor har vi fire vigtigste komponenter - *klienten på computeren*, *programmet backend-version*, *Azure meddelelse Hubs (NH)* og *Push meddelelse tjenester (PNS)* og et af følgende kan medføre beskeder hele tiden afbrudt. Flere oplysninger om denne arkitektur er tilgængelig på [Meddelelse Hubs oversigt].

Det lykkedes ikke at levere meddelelser kan opstå under den indledende test/midlertidige fase, som kan skyldes et konfigurationsproblem eller det kan opstå i fremstilling, alle eller nogle af meddelelserne kan henter mistede viser nogle dybere programmet eller messaging mønster problem. I sektionen vil nedenfor vi se på lige fra almindelige til den sjældne type, som du kan finde indlysende og nogle andre ikke så mange forskellige scenarier med mistede meddelelser. 

##<a name="azure-notifications-hub-mis-configuration"></a>Azure beskeder Hub forkert konfiguration 

Azure meddelelse Hubs behov at godkende sig selv i konteksten for den udvikler program tilladelse til at kunne sende beskeder til de respektive PNS. Dette er gjort muligt af udvikleren oprettelse af en udvikler konto med den pågældende platform (Google, Apple osv Windows) og derefter registrere deres program, hvor de kan komme legitimationsoplysninger, der skal konfigureres i portalen under meddelelse Hubs i konfigurationsafsnittet. Hvis ingen beskeder laver gennem, skal være første trin til at sikre, at de korrekte legitimationsoplysninger er konfigureret i meddelelse hubben tilsvarende dem med det program, der er oprettet under deres platform bestemte udvikler konto. Du kan finde vores [Komme i gang selvstudier] nyttigt at gennemgå denne proces i en trinvis måde. Her er nogle almindelige forkerte konfigurationer:

1. **Generelt**
 
    a) Sørg for, at navnet på hub din meddelelse (uden slåfejl) er den samme:

    - Hvor du registrerer fra klienten på computeren, 
    - Når du sender meddelelser fra backend-version,  
    - Hvor du har konfigureret PNS legitimationsoplysninger og 
    - Hvis du har konfigureret på klienten og back end-SAS-legitimationsoplysninger. 
        
    b) Sørg for, at du bruger de korrekte SAS konfiguration strenge på klienten og programmet back end. Som en tommelfingerregel, skal du bruge **DefaultListenSharedAccessSignature** på klienten og **DefaultFullSharedAccessSignature** på programmet back-end (som giver tilladelse til at kunne sende meddelelser til NH)

2. **Konfiguration af Apple Push meddelelse Service (APNS)**
 
    Du skal stadig to forskellige hubs - én for fremstilling og en anden til test formål. Det betyder, at overføre det certifikat, du skal bruge, i sandkassetilstanden miljø til en separat hub og det certifikat, du skal bruge, i fremstilling til en separat hub. Forsøg ikke at overføre forskellige typer certifikater til den samme hub, som det kan medføre besked om fejl på linje ned. Hvis du kan finde dig selv på en placering, hvor du ved et uheld har overført forskellige typer certifikat i samme hub, anbefales det at slette hubben og starte forfra. Hvis en eller anden grund, du ikke kan slette hubben derefter i det mindste, skal du slette alle eksisterende registreringer fra hubben. 

3. **Konfiguration af Google Cloud Messaging (GCM)** 

    a) Sørg for, at du aktiverer "Google Cloud-beskeder til Android" under projektet skyen. 
    
    ![][2]
    
    b) Sørg for at oprette en "Server nøgle", mens hente legitimationsoplysningerne, hvilket NH skal bruge til at godkende med GCM. 
    
    ![][3]
    
    c) Sørg for, at du har konfigureret "Project-ID" på den klient, som er et helt numeriske objekt, du kan få fra dashboardet for:
    
    ![][1]

##<a name="application-issues"></a>Problemer med programmet

1) **Koder / mærke udtryk**

Hvis du bruger mærker eller mærke udtryk til at inddele dit publikum, er det altid mulige, når du sender meddelelsen, der er ingen destination, der blev fundet baseret på de mærker/mærke udtryk, du angiver i opkaldet send. Det er bedst at gennemse dine registreringer for at sikre, at der er mærker, som når du sender meddelelsen og derefter kontrollere besked om kvittering kun fra klienter med disse registreringer. F.eks. Hvis alle dine registreringer med NH var færdige med sig mærke "Politik", og du sender en meddelelse med kode "Sports", sendes det ikke til en hvilken som helst enhed. En kompleks sag kunne involverer mærke udtryk, hvor du kun registreret med "Mærke A" eller "Mærke B", men under afsendelse af meddelelser, du målretter "Mærke A & & mærke B". I afsnittet selvbetjenings diagnosticere tip nedenfor er der måder, som du kan gennemse dine registreringer sammen med de mærker, de har. 

2) **Skabelon problemer**

Hvis du bruger skabeloner, skal du sikre dig, at du følger retningslinjerne beskrevet på [skabelon vejledning]. 

3) **Ugyldige registreringer**

Hvis hubben meddelelse blev konfigureret korrekt, og eventuelle mærker/mærke udtryk blev brugt korrekt resultatet i feltet find gyldige destinationer, hvor meddelelserne skal sendes, udløses NH fra flere behandling batches parallelt - hvert batch sende meddelelser til en række registreringer. 

> [AZURE.NOTE] Da vi gør behandling parallelt, kan vi ikke garantere den rækkefølge, hvori i meddelelser vil blive leveret. 

Nu er Azure beskeder Hub optimeret til en "på yderst én gang" model for levering af meddelelse. Det betyder, at vi forsøger ophævelse spilde, så ingen meddelelser leveres mere end én gang til en enhed. At sikre, at dette vi Gennemse registreringer, og Sørg for, at den kun én meddelelse sendes per enheds-id inden du rent faktisk sender meddelelsen til PNS. Hver batchen er sendt til PNS, som også acceptere og validere registreringer, er det muligt, at PNS registrerer en fejl med en eller flere af registreringer i en gruppe, returnerer fejlen til Azure NH og holder op med at behandle og slippe batchen helt. Dette er tilfældet med APNS som bruger en TCP værdistrøm protokol. Selvom vi er optimeret til på mest, når levering, i dette tilfælde vil vi fjerne forårsagede registreringen fra vores database, og prøv derefter igen besked om levering for resten af enheder i pågældende batch.

Du kan hente fejloplysninger for levering af mislykkede forsøg mod en registrering ved hjælp af Azure meddelelse Hubs REST API'er: [hver meddelelse Telemetri: få besked om meddelelse Telemetri](https://msdn.microsoft.com/library/azure/mt608135.aspx) og [PNS Feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx). Se [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) for eksempel kode.

##<a name="pns-issues"></a>PNS problemer

Når meddelelsen er modtaget efter den pågældende PNS er sit ansvar for at levere meddelelsen til enheden. Azure meddelelse Hubs er uden for billedet her og har ingen kontrol, når, eller hvis der skal meddelelsen blev leveret til enheden. Fordi platform besked om tjenesterne er ret robust, beskeder har tendens til at nå enhederne på et par sekunder fra PNS. Hvis PNS dog (throttling) Azure meddelelse Hubs anvende en eksponentiel tilbage fra strategi, og hvis PNS forbliver er utilgængelig for 30 min derefter har vi en politik til at udløbe og slippe disse meddelelser permanent. 

Hvis en PNS forsøger at levere en meddelelse, men enheden er offline, gemmes ved PNS for en begrænset periode meddelelsen, og leveret til enheden, når den bliver tilgængelig. Kun én seneste meddelelse til en bestemt app er gemt. Hvis flere meddelelser sendes, når enheden er offline, får hver besked om ny forudgående meddelelse til bliver kasseret. Denne funktionsmåde for at holde kun den nyeste meddelelse kaldes sammenfaldende precipitinlinjer beskeder i APNS og skjule i GCM (som bruger en collapsing nøgle). Hvis enheden forbliver offline i lang tid, slettes alle meddelelser, som blev blive gemt for den. Kilde - [APNS vejledning] & [GCM vejledning]

Med Azure meddelelse Hubs – kan du sende en samler nøgle via en HTTP-header ved hjælp af standard `SendNotification` API (fx for .NET SDK – `SendNotificationAsync`), også tager HTTP-headere, der overføres som er til de respektive PNS. 

##<a name="self-diagnose-tips"></a>Selv diagnosticere tip

Her vil vi undersøge de forskellige muligheder til at diagnosticere og root medføre problemer med at meddelelse Hub:

###<a name="verify-credentials"></a>Bekræfte legitimationsoplysninger

1. **PNS udvikler portal**

    Kontrollér dem på de respektive PNS udvikler portal (APNS, GCM, WNS osv) ved hjælp af vores [Komme i gang selvstudier].

2. **Azure klassisk portal**

    Gå til fanen Konfigurer for at gennemse og svarer til legitimationsoplysningerne med dem, der fås fra portalen PNS udvikler. 

    ![][4]

###<a name="verify-registrations"></a>Bekræfte registreringer

1. **Visual Studio**

    Hvis du bruger Visual Studio til udvikling kan du oprette forbindelse til Microsoft Azure og få vist og administrere en gruppe af Azure tjenesterne, herunder beskeder Hub fra "Server Explorer". Dette er især nyttigt til dit Udviklingscenter/testmiljø. 

    ![][9]

    Du kan få vist og administrere alle registreringer i din hub, som er fint kategoriseret til platformen, Oprindelig eller skabelon registrering, eventuelle mærker, PNS-id, registrering-id og udløbsdatoen. Du kan også redigere en registrering på farten - hvilket er nyttigt sig, hvis du vil redigere eventuelle mærker. 

    ![][8]
 
    > [AZURE.NOTE] Visual Studio-funktioner for at redigere registreringer bør kun bruges under Udviklingscenter/test med begrænset antal registreringer. Hvis der opstår behov for at løse dine registreringer ad gangen, kan du overveje at bruge funktionen eksport/Import registrering er beskrevet her - [Eksport/Import registreringer] (https://msdn.microsoft.com/library/dn790624.aspx)

2. **Service Bus explorer**

    Mange kunder Brug ServiceBus explorer beskrevet her - [ServiceBus Explorer] for at få vist og administrere deres meddelelser hub. Det er et open source-projekt, der er tilgængelige fra code.microsoft.com - [ServiceBus Explorer-kode]

###<a name="verify-message-notifications"></a>Bekræfte notifikationer om fejlmeddelelser

1. **Azure klassisk Portal**

    Du kan gå til fanen "Fejlfinding" for at sende test meddelelser til dine kunder uden at der er brug for en hvilken som helst service back-end op og kører. 

    ![][7]

2. **Visual Studio**

    Du kan også sende test beskeder fra comforts af Visual Studio:

    ![][10]

    Du kan læse mere om explorer funktioner Visual Studio meddelelse Hub Azure her - 
    
    - [Eller-Server Explorer oversigt]
    - [Eller-Server Explorer blogindlæg - 1]
    - [Eller-Server Explorer blogindlæg - 2]

###<a name="debug-failed-notifications-review-notification-outcome"></a>Fejlfinding af mislykkede meddelelser / Gennemse besked om resultatet

**EnableTestSend egenskab**

Når du sender en besked via meddelelse Hubs, har det lige bliver i kø til NH gøre behandling for at finde ud af alle destinationer og derefter til sidst NH sender den til PNS. Det betyder, når du bruger REST-API eller en af klient-SDK, vellykket returnering af opkaldet send kun betyder, at meddelelsen er sat korrekt i kø med besked om Hub. Den give ikke et overblik over, Hvad skete der, når NH til sidst har til at sende meddelelsen til PNS. Hvis din meddelelse ikke ankomme i klientenhed, er der mulighed for, at når NH forsøgte at levere meddelelsen til PNS, der opstod en fejl, f.eks. datastørrelse overskredet det maksimalt tilladte ved PNS, eller de legitimationsoplysninger, der er konfigureret i NH er ugyldige osv. For at få indsigt i PNS fejl, har vi introduceret en egenskab, der kaldes [EnableTestSend funktion]. Denne egenskab er automatisk aktiveret, når du sender testmeddelelser fra portalen eller Visual Studio-klienten og derfor giver dig mulighed at se detaljerede oplysninger om fejlfinding. Du kan bruge denne via API'er tager eksemplet med .NET SDK, hvor den er tilgængelig nu og føjes til alle klient SDK'er til sidst. For at bruge dette med RESTEN opkaldet, skal du blot tilføje en forespørgselsstrengparameter fx navnet "test" i slutningen af send opkaldet 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Eksempel (.NET SDK)*
 
Antag, at du bruger .NET SDK til at sende en oprindelig toastbeskeden:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State`vil blot stat `Enqueued` i slutningen af udførelse af uden en hvilken som helst indsigt i Hvad er der sket med din ordre. Nu kan du bruge den `EnableTestSend` booleske egenskab under initialisering af den `NotificationHubClient` og kan få detaljeret status om PNS fejl under afsendelse af meddelelsen. Send opkaldet her tager mere tid til at returnere, fordi det kun returnere når NH har leveret meddelelsen til PNS at afgøre resultatet. 
 
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
    
    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);
    
    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Eksempel på Output*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
Denne meddelelse angiver enten ugyldige legitimationsoplysninger er konfigureret i hubben meddelelse eller et problem med registreringer på hubben og anbefalede kurset ville være at slette denne registrering og lade klienten genskabe før afsendelse af meddelelsen. 
 
> [AZURE.NOTE] Bemærk, at brug af denne egenskab er meget begrænset og så du skal kun bruge dette i Udviklingscenter/testmiljø med begrænset antal registreringer. Vi kan kun sende fejlfinding beskeder til 10-enheder. Vi har også en begrænsning på behandling af fejlfinding sender skal være 10 i minuttet. 

###<a name="review-telemetry"></a>Gennemse telemetri 

1. **Bruge Azure klassisk Portal**

    Portalen gør det muligt at få et hurtigt overblik over alle aktivitet på din meddelelse Hub. 
    
    a) du kan få vist en samlet oversigt over registreringer, beskeder samt fejl per platform fra fanen "dashboard". 
    
    ![][5]
    
    b) du kan også tilføje mange andre platform bestemte målepunkter fra fanen "Skærm" for at se nærmere på en hvilken som helst PNS specifikke fejl, der skal returneres, når NH forsøger at sende meddelelsen til PNS særligt. 
    
    ![][6]
    
    c) du skal starte med at gennemgå **Indgående meddelelser**, **Registrering handlinger**, **Vellykket beskeder** og gå derefter til hver platform tab for at gennemse PNS fejlene. 
    
    d) Hvis du har meddelelse hubben forkert konfigureret med indstillinger for proxygodkendelse får du vist PNS godkendelsesfejl. Dette er en god angivelse til at kontrollere PNS legitimationsoplysninger. 

2) **Programmeringsmæssig adgang**

Få mere at vide her- 

- [Programmeringsmæssig Telemetri Access]
- [Telemetri adgang via API'er eksempel] 

> [AZURE.NOTE] Flere telemetri relaterede funktioner som **Eksport/Import registreringer**, **Telemetri adgang via API'er** osv er kun tilgængelig i Standard lag. Hvis du forsøger at bruge disse funktioner, hvis du er i ledig eller grundlæggende niveau så får du Undtagelsesmeddelelsen herom prøveversionen af SDK og en HTTP 403 (forbudt), når du bruger dem direkte fra REST API'er. Sørg for, at du har flyttet op til Standard klasse via Azure klassisk Portal.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[Meddelelse om Hubs oversigt]: notification-hubs-push-notification-overview.md
[Introduktionsselvstudier]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Skabelon vejledning]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNS vejledning]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM vejledning]: http://developer.android.com/google/gcm/adv.html
[Eksport/Import registreringer]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus Explorer]: http://msdn.microsoft.com/library/dn530751.aspx
[ServiceBus Explorer-kode]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Eller-Server Explorer oversigt]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Eller-Server Explorer blogindlæg - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Eller-Server Explorer blogindlæg - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend funktion]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Programmeringsmæssig Telemetri Access]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Telemetri adgang via API'er eksempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

 