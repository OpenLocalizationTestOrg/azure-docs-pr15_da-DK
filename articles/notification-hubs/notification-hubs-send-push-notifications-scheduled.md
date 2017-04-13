<properties
    pageTitle="Sådan sender planlagte meddelelser om | Microsoft Azure"
    description="Dette emne beskrives ved hjælp af planlagt meddelelser med Azure meddelelse Hubs."
    services="notification-hubs"
    documentationCenter=".net"
    keywords="Push-beskeder, push besked om planlægning af pushmeddelelser"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="how-to-send-scheduled-notifications"></a>Sådan: Send planlagt beskeder


##<a name="overview"></a>Oversigt

Hvis du har et scenarie, hvor du vil sende en meddelelse på et tidspunkt i fremtiden, men ikke har en nem måde at vækker din back end-kode til at sende meddelelsen. Standard niveau meddelelse Hubs understøtter en funktion, der gør det muligt at planlægge beskeder op til 7 dage i fremtiden.

Når du sender en meddelelse, skal du blot bruge klassen [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) i meddelelse Hubs SDK som vist i følgende eksempel:

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

Du kan også annullere en tidligere planlagte meddelelse ved hjælp af dens notificationId:

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

Der er ingen begrænsninger på antallet af planlagte meddelelser, du kan sende.