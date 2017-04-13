<properties
    pageTitle="Routing og mærke udtryk"
    description="Dette emne forklares routing og mærke udtryk for Azure meddelelse hubs."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>Routing og mærke udtryk

##<a name="overview"></a>Oversigt

Mærke udtryk giver mulighed for at target bestemte typer enheder eller mere specifikt registreringer, når du sender en meddelelse om opslagsnål gennem meddelelse Hubs.


## <a name="targeting-specific-registrations"></a>Målretning af bestemte registreringer

Den eneste måde at target målrette bestemte besked om registreringer er knytte mærker til dem, derefter disse mærker. Som beskrevet i [Administration af registrering](notification-hubs-push-notification-registration-management.md), for at modtage pushmeddelelser har en app til at registrere enhed styr på en meddelelse om hub. Når der oprettes en registrering på en meddelelse om-hub, kan programmet back-end sende pushmeddelelser til den.
Programmet back-end kan vælge registreringer, der skal målet, med en bestemt meddelelse på følgende måder:

1. **Transmittere**: alle registreringer i meddelelse hubben modtager meddelelsen.
2. **Mærke**: alle registreringer, der indeholder den angivne kode modtager meddelelsen.
3. **Mærke udtryk**: alle registreringer, hvis sæt mærker svarer til det angivne udtryk modtager meddelelsen.

## <a name="tags"></a>Mærker

Et mærke kan være en streng, op til 120 tegn, der indeholder alfanumeriske og følgende ikke-alfanumeriske tegn: '_', ‘@’, '#' '. ',':', '-'. I følgende eksempel viser et program, hvor du kan modtage toast beskeder om bestemte musik grupper. I dette scenario er en nem måde at distribuere meddelelser til etiket registreringer med mærker, der repræsenterer de forskellige felter, som i det følgende billede.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

På dette billede meddelelsen mærket **Beatles** når kun tablet, som registreret med mærket **Beatles**.

Du kan finde flere oplysninger om oprettelse af registreringer for mærker i [Registrering i administration](notification-hubs-push-notification-registration-management.md).

Du kan sende beskeder til mærker ved hjælp af metoderne send beskeder af den `Microsoft.Azure.NotificationHubs.NotificationHubClient` klasse i [Microsoft Azure meddelelse Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. Du kan også bruge Node.js eller Push-beskeder REST API'er.  Her er et eksempel, ved hjælp af SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Mærker behøver ikke at være allerede klargjort og kan referere til flere app-specifikke koncepter. For eksempel kan brugere af dette eksempel-program kommentere bånd og ønsker at modtage toasts, ikke kun for kommentarer i deres foretrukne bånd, men også for alle kommentarer fra deres venner, uanset det bånd, som de kommentere. Følgende billede viser et eksempel på dette scenarie:



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Bent er interesseret i opdateringer til Beatles på dette billede, og Bob er interesseret i opdateringer til Wailers. BOB er også interesseret i Charlies kommentarer, og Charlie er interesseret i Wailers. Når der sendes en besked til Charlies kommentar på Beatles, får både Bent og Bob den.

Mens du kan koder flere problemstillinger i mærker (eksempelvis "band_Beatles" eller "follows_Charlie"), er mærker enkel strenge og ikke egenskaber med værdier. En registrering matches kun på tilstedeværelsen eller fravær af et bestemt mærke.

Du kan finde en fuldstændig trinvist selvstudium om, hvordan du bruge mærker til at sende til interesse grupper [Bryd nyheder](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).


## <a name="using-tags-to-target-users"></a>Brug af mærker til destinationen brugere

Der er en anden måde at bruge mærker til at identificere alle enhederne for en bestemt bruger. Registreringer kan være mærket med et mærke, der indeholder et bruger-id, som i det følgende billede:


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

På dette billede når den meddelelse, der er mærket uid:Alice alle registreringer mærket uid:Alice; Derfor skal alle Alices enheder.


##<a name="tag-expressions"></a>Mærke udtryk

Der er tilfælde, hvor en meddelelse om har målrette en række registreringer, der er identificeret ikke som et mærke fra, men ved et boolesk udtryk på mærker.

Overvej et sports-program, der sender en påmindelse til alle på forhånd om en game mellem rød Sox og Cardinals. Hvis appen klienten registrerer mærker om interesse for teams og placering, skal meddelelsen målrettes til alle på forhånd, hvem der er interesseret i rød Sox eller Cardinals. Denne betingelse kan udtrykkes med følgende booleske udtryk:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Mærke udtryk kan indeholde alle booleske operatorer, såsom & (& &), eller (|), og ikke (!). De kan også indeholde parenteser. Mærke udtryk er begrænset til 20 mærker, hvis de indeholder kun ORs; Ellers er de begrænset til 6 mærker.

Her er et eksempel for at sende meddelelser med mærke udtryk ved hjælp af SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
